# IMAGE_IMAGES.md

IMAGE_IMAGES.md is an open-source Markdown + YAML specification for passing structured image-generation requests to AI image models and agents.

The canonical specification is [IMAGE_IMAGES.md](IMAGE_IMAGES.md).

## What This Is

This repository defines a practical format for describing images before generation. The format is meant to be:

- readable by humans
- easy for agents to parse
- portable across models such as SDXL, Flux, Midjourney, DALL-E, and Imagen
- extensible for medical, scientific, product, cinematic, and technical imagery
- safe for synthetic medical and educational image workflows

The main idea is simple: write a Markdown file that gives the agent instructions, then include a structured YAML `image_spec` block that acts as the source of truth.

## Complete Agent Example

This is the kind of `CREATE_IMAGES.md` or `IMAGE_IMAGES.md` file you can pass to an image-generation agent.

````markdown
# CREATE_IMAGES.md

## Task

Generate one synthetic product-render image from the structured specification below.

The image-generation agent must:

- Treat the YAML block as the source of truth.
- Build the final prompt from the `prompt` section.
- Use `diffusion` parameters only when the target model supports them.
- Preserve unsupported fields as metadata.
- Follow all `consistency_rules` and `safety_constraints`.
- Return the generated image, final prompt, negative prompt, model parameters used, and validation notes.

## Image Specification

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

## Prompt Assembly

Build the final positive prompt in this order:

1. `prompt.core`
2. `prompt.modifiers`
3. `prompt.style_stack`
4. `prompt.quality_stack`
5. `prompt.composition_stack`

Final positive prompt:

```text
studio product image of matte black wireless headphones, three-quarter angle, clean silhouette, softbox reflections, commercial product photography, sharp edges, accurate materials, centered, square crop
```

Final negative prompt:

```text
logo, text, warped earcups, extra cables, clutter
```

## Model Parameters

Use these parameters when supported by the target model:

```yaml
model_parameters:
  model_family: flux
  aspect_ratio: "1:1"
  steps: 28
  seed: 3007
```

## Expected Output

A square synthetic product-render image showing matte black wireless headphones on a minimal studio tabletop. The headphones should look symmetrical, realistic, sharply rendered, and free of logos or text.

## Validation Checklist

- Product is recognizable as wireless headphones.
- Left and right earcups are symmetrical.
- Materials look like soft-touch plastic, brushed aluminum, and mesh fabric.
- Lighting produces controlled softbox reflections.
- No logo, text, clutter, extra cables, or warped geometry is present.
- Output is labeled as synthetic.
````

## How To Use It

1. Create a Markdown file like the example above.
2. Put the complete YAML request inside the `Image Specification` section.
3. Give the Markdown file to an agent, adapter, or image-generation workflow.
4. The adapter validates the YAML and assembles the final prompt.
5. Supported parameters are sent to the image model.
6. Unsupported parameters are kept as metadata.
7. The output is checked against the validation checklist.

## Core Schema

The core object is `image_spec`.

```yaml
image_spec:
  version:
  intent:
  model_family:
  output_type:
  subject:
  scene:
  composition:
  camera:
  lighting:
  color_palette:
  materials:
  style:
  mood:
  environment:
  render_quality:
  aspect_ratio:
  diffusion:
    steps:
    cfg_scale:
    sampler:
    seed:
    denoise_strength:
  prompt:
    core:
    modifiers:
    style_stack:
    quality_stack:
    composition_stack:
    negative:
  consistency_rules:
  safety_constraints:
  output_requirements:
```

## Medical Extension

Medical images use the same core structure plus `medical_extension`.

Medical examples must be synthetic, non-diagnostic, and not derived from real patient data.

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

Recommended medical label:

```text
Synthetic image. Non-diagnostic. Not derived from real patient data. For education, research, simulation, or software testing only.
```

## Repository Layout

```text
IMAGE_IMAGES.md/
|-- README.md
|-- IMAGE_IMAGES.md
|-- LICENSE
|-- CHANGELOG.md
|-- CONTRIBUTING.md
|-- .gitattributes
|-- schemas/
|   |-- image_spec.schema.yaml
|   `-- medical_extension.schema.yaml
|-- examples/
|   |-- cinematic_portrait.yaml
|   `-- synthetic_chest_xray.yaml
|-- docs/
|   `-- versioning.md
`-- extensions/
    |-- radiology.md
    |-- xray.md
    |-- ct.md
    |-- mri.md
    |-- ultrasound.md
    |-- histopathology.md
    `-- oct.md
```

## GitHub Language Detection

The `.gitattributes` file forces GitHub Linguist to count Markdown, YAML, and JSON files in the repository language summary.

```gitattributes
*.md linguist-language=Markdown linguist-detectable=true linguist-documentation=false
*.yaml linguist-language=YAML linguist-detectable=true linguist-documentation=false
*.yml linguist-language=YAML linguist-detectable=true linguist-documentation=false
schemas/*.json linguist-language=JSON linguist-detectable=true linguist-documentation=false
```

## Development

- Use semantic versioning.
- Prefer small commits over monolithic rewrites.
- Keep schemas stable.
- Maintain backwards compatibility when possible.
- Add examples when changing schema behavior.
- Record user-visible changes in `CHANGELOG.md`.

## License

Apache License 2.0. Copyright 2026 Eduardo J. Barrios.
