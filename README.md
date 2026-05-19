# IMAGE_IMAGES.md

IMAGE_IMAGES.md is an open-source specification for describing AI-generated images with structured prompts, diffusion parameters, metadata, validation rules, and domain-specific extensions.

The canonical specification is [IMAGE_IMAGES.md](IMAGE_IMAGES.md).

## Overview

This repository defines a reusable format for image-generation requests. The format is designed to be readable by humans, validated by software, and adapted to multiple image-generation systems.

The specification supports:

- General image generation
- Cinematic and artistic imagery
- Product and technical rendering
- Scientific and educational visuals
- Synthetic medical imaging
- Radiography and radiology workflows
- Modality-specific extensions for X-ray, CT, MRI, ultrasound, OCT, histopathology, PET, dermoscopy, and endoscopy

IMAGE_IMAGES.md is not a model, inference engine, clinical product, diagnostic tool, DICOM implementation, or image dataset. It is a specification for describing what should be generated, how it should be constrained, and how the output should be validated.

## Specification Model

The specification is organized around a core `image_spec` object. This object describes the requested image, target model family, output type, visual structure, prompt stack, diffusion settings, safety constraints, and output requirements.

Specialized domains are added through extension objects. For example, medical imaging uses `medical_extension`, which adds modality, anatomy, acquisition, pathology, visual characteristics, safety, and validation fields.

This keeps the architecture modular:

- The core schema remains stable.
- Extensions can evolve independently.
- Model adapters can translate the same spec into SDXL, Flux, Midjourney, DALL-E, Imagen, or future systems.
- Medical imaging fits naturally into the same framework as cinematic, product, scientific, and technical image generation.

## Minimal Example

```yaml
image_spec:
  version: "0.1.0"
  intent: product_render
  model_family: flux
  output_type: image
  subject: "matte black wireless headphones"
  scene: "minimal studio tabletop"
  composition: "three-quarter product angle, centered"
  lighting: "large softbox reflection with controlled highlight edges"
  color_palette:
    - matte black
    - cool gray
    - white
  materials:
    - soft-touch plastic
    - brushed aluminum
    - mesh fabric
  render_quality: "sharp edges, clean reflections, realistic materials"
  aspect_ratio: "1:1"
  diffusion:
    steps: 28
    cfg_scale: null
    sampler: null
    seed: 3007
    denoise_strength: null
  prompt:
    core: "studio product image of matte black wireless headphones"
    modifiers:
      - three-quarter angle
      - clean silhouette
      - softbox reflections
    style_stack:
      - commercial product photography
    quality_stack:
      - sharp edges
      - accurate materials
    composition_stack:
      - centered
      - square crop
    negative: "logo, text, warped earcups, extra cables, clutter"
  consistency_rules:
    - "left and right earcups must be symmetrical"
  safety_constraints:
    - "no trademarked logo"
  output_requirements:
    label: "Synthetic image"
```

## How The Specification Works

An IMAGE_IMAGES.md document separates image generation into structured layers.

| Layer | Purpose |
| --- | --- |
| `intent` | Declares why the image is being generated. |
| `model_family` | Identifies the preferred target model family or adapter. |
| `subject` | Defines the primary object, scene, anatomy, environment, or concept. |
| `composition` | Controls framing, perspective, layout, and view. |
| `camera` | Describes photographic or virtual-camera behavior when applicable. |
| `lighting` | Describes light, exposure, contrast, or equivalent modality signal. |
| `materials` | Describes surface, tissue, object, or physical representation. |
| `diffusion` | Stores seed, sampler, steps, CFG scale, and denoise strength. |
| `prompt` | Builds the final positive and negative prompt from ordered stacks. |
| `consistency_rules` | Defines constraints that keep the output coherent. |
| `safety_constraints` | Defines required safety and misuse-prevention rules. |
| `output_requirements` | Defines labels, metadata, resolution, and delivery expectations. |

## Prompt Architecture

The prompt system is designed so adapters can generate model-specific prompts without losing the structured source data.

| Prompt Component | Description |
| --- | --- |
| Core Prompt | Main image request and subject. |
| Modifier Tokens | Short refinements for details and attributes. |
| Weighted Tokens | Optional importance weights where supported. |
| Style Stack | Artistic, photographic, rendering, or modality style. |
| Quality Stack | Detail, fidelity, clarity, realism, or noise requirements. |
| Composition Stack | Framing, layout, view, plane, or crop. |
| Medical Constraint Stack | Synthetic-only, non-diagnostic, anatomy-aware, and modality-aware constraints. |
| Negative Prompt | Content, artifacts, unsafe claims, or visual errors to avoid. |

Safety constraints take precedence over style and prompt preferences. Extension fields take precedence over generic prose. If an adapter cannot support a field, it should preserve the unsupported field as metadata and report the conversion loss.

## Using The Specification With Image Models

IMAGE_IMAGES.md is meant to be passed to an image-generation workflow as structured source data, not as an unprocessed wall of text. A typical implementation reads a YAML image spec, validates it, converts the structured fields into a model-specific prompt, sends the prompt and supported parameters to the model, and stores the original spec as metadata.

Recommended workflow:

1. Write or load an `image_spec` YAML document.
2. Validate required fields, safety constraints, and extension rules.
3. Build a positive prompt from `prompt.core`, `modifiers`, `style_stack`, `quality_stack`, and `composition_stack`.
4. Build a negative prompt from `prompt.negative` plus any extension-specific negative prompt.
5. Map supported `diffusion` fields into the target model request.
6. Preserve unsupported fields as metadata instead of discarding them.
7. Generate the image.
8. Validate the output against `consistency_rules`, `safety_constraints`, and modality-specific checks.
9. Store the generated image with the original YAML spec, model name, seed, adapter version, and validation result.

### Prompt Assembly

For most models, the final prompt can be assembled in this order:

```text
{prompt.core},
{prompt.modifiers},
{prompt.style_stack},
{prompt.quality_stack},
{prompt.composition_stack},
{extension constraints when applicable}
```

The negative prompt can be assembled as:

```text
{prompt.negative},
{medical_extension.medical_negative_prompt when applicable},
{safety exclusions}
```

Example assembled prompt:

```text
studio product image of matte black wireless headphones, three-quarter angle, clean silhouette, softbox reflections, commercial product photography, sharp edges, accurate materials, centered, square crop
```

Example negative prompt:

```text
logo, text, warped earcups, extra cables, clutter
```

### Adapter Mapping

Different image models expose different controls. Implementations should map only the fields the target model supports and keep the rest as metadata.

| IMAGE_IMAGES.md Field | SDXL / Stable Diffusion | Flux | Midjourney | DALL-E / Imagen |
| --- | --- | --- | --- | --- |
| `prompt.core` | Positive prompt | Prompt text | Prompt text | Prompt text |
| `prompt.negative` | Negative prompt | Negative text if supported | Usually omitted or converted to prose | Converted to natural-language exclusions |
| `diffusion.steps` | Steps | Steps if exposed | Not supported directly | Not supported directly |
| `diffusion.cfg_scale` | CFG scale | Guidance if exposed | Not supported directly | Not supported directly |
| `diffusion.sampler` | Sampler/scheduler | Scheduler if exposed | Not supported directly | Not supported directly |
| `diffusion.seed` | Seed | Seed if exposed | Seed parameter if supported | Usually not supported |
| `aspect_ratio` | Width/height mapping | Width/height mapping | Aspect parameter | Size/aspect option if exposed |
| `safety_constraints` | Pre-generation validation | Pre-generation validation | Prompt policy and metadata | Prompt policy and metadata |

### Pseudocode

```python
spec = load_yaml("examples/synthetic_chest_xray.yaml")

validate_schema(spec)
validate_safety(spec)

positive_prompt = assemble_positive_prompt(spec)
negative_prompt = assemble_negative_prompt(spec)
model_params = map_diffusion_params(spec["image_spec"]["diffusion"], target="sdxl")

image = generate_image(
    prompt=positive_prompt,
    negative_prompt=negative_prompt,
    **model_params,
)

validation_result = validate_output(image, spec)
save_with_metadata(image, spec, validation_result)
```

### Direct Text Models

Some systems do not accept seeds, samplers, CFG scale, or negative prompts. For those systems, convert the structured fields into a single natural-language instruction and keep the original YAML beside the output.

Example:

```text
Generate a synthetic non-diagnostic PA chest X-ray for radiology education. The image should show an adult thorax with full lungs visible, plausible ribs and clavicles, clear mediastinal silhouette, radiographic grayscale density, adequate penetration, and no text overlays. Do not include patient identifiers, hospital labels, DICOM metadata, accession numbers, fake reports, or any claim that this is a real clinical image.
```

### Medical Model Usage

When using medical examples, the adapter must enforce `medical_extension` before generation. If `synthetic_only`, `non_diagnostic`, `no_patient_identity`, or `no_real_patient_data` is missing or false, the request should fail validation.

Medical generation should always include:

- Synthetic-only language in the final prompt.
- Non-diagnostic labeling in metadata or visible documentation.
- No real patient data.
- No fake report or fake DICOM metadata.
- A validation result for anatomy, modality, pathology, artifact realism, and safety.

## Medical Imaging Extension

Medical imaging is implemented as a governed extension instead of a separate standard. This allows synthetic clinical imagery to use the same prompt, diffusion, validation, and metadata architecture as other image types.

The medical extension supports:

- Radiografias and synthetic X-rays
- Radiology education workflows
- CT, MRI, ultrasound, OCT, histopathology, PET, dermoscopy, and endoscopy
- Modality-aware prompting
- Anatomy-aware constraints
- Pathology-aware prompting
- Acquisition-aware generation
- Artifact simulation
- Non-diagnostic labeling

Minimal medical structure:

```yaml
medical_extension:
  enabled: true
  synthetic_only: true
  non_diagnostic: true
  modality: xray
  body_region: chest
  anatomy:
    - lungs
    - ribs
    - clavicles
    - heart silhouette
    - diaphragm
  projection_or_view: PA
  pathology:
    presence: absent
  acquisition:
    protocol: "upright chest radiograph"
    exposure: "adequate penetration"
  visual_characteristics:
    density_pattern: "air dark, bone bright, soft tissue intermediate"
    noise_profile: "mild detector noise"
  clinical_constraints:
    anatomical_consistency: required
    pathology_consistency: required
    acquisition_plausibility: required
    no_patient_identity: required
    no_real_patient_data: required
  medical_negative_prompt: "patient identifiers, accession number, institution name, report text, fake DICOM"
  validation:
    anatomy_check: required
    modality_check: required
    pathology_check: required
    artifact_check: required
    safety_check: required
```

Medical images generated from this specification must be synthetic, non-diagnostic, and not derived from real patient data.

Recommended medical label:

```text
Synthetic image. Non-diagnostic. Not derived from real patient data. For education, research, simulation, or software testing only.
```

## Validation

The specification includes validation concepts for both general and medical imagery.

General validation checks:

- Required schema fields are present.
- Intent is explicit.
- Prompt stacks do not conflict.
- Output requirements are feasible.
- Safety constraints are present.
- Unsupported model fields are reported.

Medical validation checks:

- Anatomy is plausible.
- Modality appearance is consistent.
- Pathology, when present, is internally coherent.
- Acquisition metadata is plausible.
- Artifacts are realistic for the modality.
- Safety checks pass before generation.

Medical content should fail validation if it includes real patient data, patient identifiers, fake reports, fake DICOM exports, fabricated hospital studies, or claims of real diagnosis.

## Repository Layout

```text
IMAGE_IMAGES.md/
├── README.md
├── IMAGE_IMAGES.md
├── LICENSE
├── CHANGELOG.md
├── CONTRIBUTING.md
├── .gitattributes
├── schemas/
│   ├── image_spec.schema.yaml
│   └── medical_extension.schema.yaml
├── examples/
│   ├── cinematic_portrait.yaml
│   └── synthetic_chest_xray.yaml
├── docs/
│   └── versioning.md
└── extensions/
    ├── radiology.md
    ├── xray.md
    ├── ct.md
    ├── mri.md
    ├── ultrasound.md
    ├── histopathology.md
    └── oct.md
```

## GitHub Language Detection

The repository includes a `.gitattributes` file so GitHub Linguist recognizes the project as Markdown and YAML instead of leaving specification files out of the language summary.

```gitattributes
*.md linguist-language=Markdown linguist-detectable=true linguist-documentation=false
*.markdown linguist-language=Markdown linguist-detectable=true linguist-documentation=false
*.yaml linguist-language=YAML linguist-detectable=true linguist-documentation=false
*.yml linguist-language=YAML linguist-detectable=true linguist-documentation=false
schemas/*.json linguist-language=JSON linguist-detectable=true linguist-documentation=false
schemas/*.yaml linguist-language=YAML linguist-detectable=true linguist-documentation=false
schemas/*.yml linguist-language=YAML linguist-detectable=true linguist-documentation=false
examples/*.yaml linguist-language=YAML linguist-detectable=true linguist-documentation=false
examples/*.yml linguist-language=YAML linguist-detectable=true linguist-documentation=false
docs/*.md linguist-language=Markdown linguist-detectable=true linguist-documentation=false
extensions/*.md linguist-language=Markdown linguist-detectable=true linguist-documentation=false
```

This improves repository readability on GitHub and makes the language bar reflect that the project is primarily a Markdown specification with YAML schemas and examples.

## Development Philosophy

This repository is intended to evolve incrementally as an open-source standard.

Project rules:

- Use semantic versioning.
- Prefer small commits over monolithic rewrites.
- Keep schemas stable.
- Maintain backwards compatibility when possible.
- Version modality extensions independently when needed.
- Add examples when changing schema behavior.
- Record user-visible changes in `CHANGELOG.md`.
- Keep medical safety rules strict and explicit.

## Contributing

Contributions should be focused, reviewable, and compatible with the existing schema architecture.

Good contribution types include:

- Schema clarifications
- New examples
- Model adapter notes
- Validation rules
- Modality-specific extensions
- Documentation improvements
- Safety and misuse-prevention improvements

Before proposing a breaking schema change, open an issue describing the motivation, compatibility impact, migration path, and affected extensions.

## License

Apache License 2.0. Copyright 2026 Eduardo J. Barrios.
