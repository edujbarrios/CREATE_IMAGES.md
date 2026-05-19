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
