# Create_images.md

## 1. Overview

Create_images is a structured, extensible specification for describing AI-generated images with precision, reproducibility, portability, and validation across diffusion and image-generation systems.

The specification defines a common image description format that can be implemented by prompt builders, validators, render orchestration systems, educational tools, research pipelines, and model adapters. It supports general image generation, cinematic imagery, scientific imagery, product rendering, technical rendering, educational visuals, medical imaging, radiography, radiology, and synthetic clinical imagery.

Medical imaging is implemented as a governed extension of the same core schema rather than as a separate incompatible format. This allows synthetic X-rays, CT, MRI, ultrasound, OCT, histopathology, PET, dermoscopy, and endoscopy specifications to inherit the same prompting, metadata, diffusion, validation, safety, and compatibility architecture used by non-medical imagery.

## 2. Purpose

The purpose of Create_images is to provide:

- A reusable specification format for AI-generated images.
- A machine-readable schema for structured prompting.
- A portable metadata layer for diffusion parameters.
- A validation framework for image intent, constraints, and quality.
- A modality-aware extension mechanism for specialized domains.
- A responsible framework for synthetic medical and educational imagery.

Create_images is not a model, inference engine, clinical tool, DICOM implementation, or diagnostic system. It is a specification for describing intended generated images and the constraints under which those images should be produced.

## 3. Design Philosophy

Create_images follows protocol-oriented design principles:

- Stable core schema: the base fields should remain predictable across releases.
- Extension modularity: domain-specific requirements belong in versioned extensions.
- Explicit intent: the image purpose must be declared before generation.
- Portable prompting: prompts should be translatable across SDXL, Flux, Midjourney, DALL-E, Imagen, and future systems.
- Metadata preservation: generation parameters, safety constraints, and validation results should be retained.
- Reproducibility: seeds, samplers, model family, aspect ratio, and prompt stacks should be recorded when available.
- Backwards compatibility: compatible schema evolution is preferred over breaking changes.
- Incremental development: repository changes should be small, reviewable, and documented.

## 4. Core Concepts

| Concept | Definition |
| --- | --- |
| Image Spec | A complete structured request for one generated image or image set. |
| Core Schema | The shared fields used by all image categories. |
| Extension | A versioned module that adds domain-specific fields. |
| Prompt Stack | Ordered prompt components combined according to precedence rules. |
| Validation Rule | A machine- or human-checkable condition for acceptability. |
| Modality | A domain-specific image class, such as X-ray, MRI, CT, product render, or cinematic still. |
| Adapter | Software that maps Create_images fields to a target model API or prompt format. |

## 5. General Image Schema

The canonical schema is represented in YAML for readability. Implementations may also provide JSON Schema, OpenAPI components, or strongly typed SDK definitions.

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

### Field Definitions

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `image_spec.version` | string | Yes | Semantic version of the Create_images schema used by the document. |
| `image_spec.intent` | string | Yes | Purpose of the image, such as `cinematic`, `product_render`, `education`, `technical`, `medical_synthetic`, or `scientific`. |
| `image_spec.model_family` | string | No | Target or preferred generation family, such as `sdxl`, `flux`, `midjourney`, `dalle`, `imagen`, or `model_agnostic`. |
| `image_spec.output_type` | string | Yes | Expected output class, such as `image`, `render`, `diagram`, `radiograph`, `slide`, or `illustration`. |
| `image_spec.subject` | object or string | Yes | Primary entity or anatomical, product, environmental, or conceptual subject. |
| `image_spec.scene` | object or string | No | Context around the subject. |
| `image_spec.composition` | object or string | No | Framing, layout, perspective, subject placement, and visual hierarchy. |
| `image_spec.camera` | object | No | Lens, focal length, sensor behavior, viewpoint, depth of field, or virtual camera properties. |
| `image_spec.lighting` | object or string | No | Light sources, direction, intensity, contrast, exposure, and shadows. |
| `image_spec.color_palette` | array or string | No | Intended color range or grayscale mapping. |
| `image_spec.materials` | array or string | No | Physical material properties, surface finish, texture, reflectance, or tissue representation. |
| `image_spec.style` | array or string | No | Visual style constraints. |
| `image_spec.mood` | string | No | Emotional or atmospheric tone for non-clinical images. |
| `image_spec.environment` | object or string | No | Location, background, operating context, or surrounding medium. |
| `image_spec.render_quality` | object or string | No | Resolution, detail level, realism target, noise tolerance, and finishing quality. |
| `image_spec.aspect_ratio` | string | No | Output aspect ratio, such as `1:1`, `4:3`, `16:9`, or `3:2`. |
| `image_spec.diffusion` | object | No | Diffusion-specific generation parameters. |
| `image_spec.prompt` | object | Yes | Prompt architecture used to construct final prompts. |
| `image_spec.consistency_rules` | array | No | Constraints that preserve internal coherence. |
| `image_spec.safety_constraints` | array | Yes | Safety rules applicable to the image request. |
| `image_spec.output_requirements` | object | No | File, resolution, labeling, metadata, or post-processing requirements. |

## 6. Medical Imaging Extension Schema

Medical imaging is a first-class extension that maps clinical image-generation requirements into the same structured prompt and validation system. It is intended for synthetic, non-diagnostic educational, research, simulation, testing, and documentation workflows.

```yaml
medical_extension:
  enabled: true
  synthetic_only: true
  non_diagnostic: true
  modality:
  body_region:
  anatomy:
  anatomical_plane:
  projection_or_view:
  laterality:
  pathology:
    name:
    presence:
    severity:
    distribution:
    diagnostic_features:
  acquisition:
    protocol:
    contrast:
    slice_thickness:
    reconstruction_kernel:
    field_strength:
    kvp:
    exposure:
    probe_type:
    magnification:
  visual_characteristics:
    density_pattern:
    signal_intensity:
    echogenicity:
    staining_pattern:
    noise_profile:
    artifact_profile:
  clinical_constraints:
    anatomical_consistency:
    pathology_consistency:
    acquisition_plausibility:
    no_patient_identity:
    no_real_patient_data:
  medical_negative_prompt:
  validation:
    anatomy_check:
    modality_check:
    pathology_check:
    artifact_check:
    safety_check:
```

### Medical Extension Field Definitions

| Field | Description |
| --- | --- |
| `enabled` | Enables medical extension semantics. |
| `synthetic_only` | Requires the image to be synthetic and not derived from real patient data. |
| `non_diagnostic` | Declares the image unsuitable for diagnosis, care decisions, or clinical documentation. |
| `modality` | Imaging type: `xray`, `ct`, `mri`, `ultrasound`, `oct`, `histopathology`, `pet`, `dermoscopy`, or `endoscopy`. |
| `body_region` | Broad region such as chest, brain, abdomen, retina, skin, colon, or lung tissue. |
| `anatomy` | Target anatomy and required structures. |
| `anatomical_plane` | Axial, sagittal, coronal, oblique, transverse, longitudinal, or not applicable. |
| `projection_or_view` | Radiographic view such as PA, AP, lateral, oblique, portable AP, mammographic CC, or MLO. |
| `laterality` | Left, right, bilateral, midline, or not applicable. |
| `pathology` | Optional synthetic abnormality definition. |
| `acquisition` | Simulated acquisition settings appropriate to the modality. |
| `visual_characteristics` | Modality-specific appearance controls. |
| `clinical_constraints` | Rules that enforce plausibility and prevent misuse. |
| `medical_negative_prompt` | Medical-specific exclusions. |
| `validation` | Required medical validation checks. |

The extension explicitly supports radiografias, radiology workflows, synthetic X-rays, modality-aware prompting, anatomy-aware constraints, pathology-aware prompting, and acquisition-aware generation.

### Medical Mapping

Medical generation maps core fields as follows:

| Core Field | Medical Mapping |
| --- | --- |
| `intent` | Usually `medical_synthetic`, `education`, `research`, or `simulation`. |
| `subject` | Anatomy, body region, or specimen. |
| `scene` | Acquisition context or clinical imaging view, not a real hospital event. |
| `composition` | Projection, plane, field of view, crop, and orientation. |
| `camera` | Replaced or supplemented by acquisition geometry. |
| `lighting` | Replaced by modality contrast, signal, density, or staining behavior. |
| `color_palette` | Grayscale density, false color, stain palette, or modality-specific color map. |
| `materials` | Tissue, bone, air, fluid, contrast, cellular structures, or pathology representation. |
| `style` | Must avoid implying a real clinical study. |
| `safety_constraints` | Must include synthetic-only and non-diagnostic constraints. |

### Medical Image Semantics

- Grayscale density interpretation: radiography and CT should represent air, soft tissue, fat, bone, fluid, and metal with plausible relative attenuation.
- Radiographic contrast: X-ray prompts should specify projection, exposure, penetration, scatter, and contrast relationships.
- Tissue representation: MRI, ultrasound, histopathology, OCT, and dermoscopy should use modality-appropriate visual signals rather than photographic tissue rendering.
- Diagnostic realism constraints: anatomy and pathology may be realistic, but the output must remain labeled synthetic and non-diagnostic.
- Artifact simulation: supported artifacts include motion, quantum noise, beam hardening, metal artifact, ultrasound speckle, MRI ghosting, staining variation, compression artifacts, and limited field-of-view effects.

## 7. Prompt Architecture

Create_images separates prompt construction into ordered components.

| Component | Purpose |
| --- | --- |
| Core Prompt | Defines the primary subject, intent, modality, and scene. |
| Modifier Tokens | Short descriptors that refine details. |
| Weighted Tokens | Explicit importance weights when supported by the target model. |
| Style Stack | Visual style, rendering style, or modality style. |
| Quality Stack | Detail, resolution, clarity, noise, and fidelity requirements. |
| Composition Stack | Framing, perspective, view, anatomical plane, or layout. |
| Medical Constraint Stack | Synthetic, non-diagnostic, modality-aware, anatomy-aware, and acquisition-aware rules. |
| Negative Prompt | Explicit exclusions, artifacts to avoid, misuse restrictions, and model-specific suppression terms. |

### Precedence

1. Safety constraints override all prompt fields.
2. Extension constraints override general style preferences.
3. Core prompt defines the semantic target.
4. Composition stack controls spatial layout.
5. Quality stack controls output fidelity.
6. Style stack controls appearance only when it does not conflict with modality or safety.
7. Negative prompt suppresses unwanted content and unsafe interpretations.

### Weighting

Weighted tokens may use adapter-specific syntax:

- SDXL: `(term:1.2)` or prompt weighting supported by the implementation.
- Flux: natural-language weighting or adapter metadata.
- Midjourney: `term::weight` and parameter syntax.
- DALL-E: natural-language emphasis; explicit numeric weights should be converted to prose.
- Imagen: natural-language emphasis; numeric weights should be treated as implementation metadata.

### Conflict Resolution

When fields conflict, implementations should apply this order:

1. Reject unsafe or non-compliant requests.
2. Prefer explicit extension fields over inferred prompt text.
3. Prefer structured fields over free-text modifiers.
4. Drop style terms that contradict modality realism.
5. Emit a validation warning when lossy adaptation is required.

### Inheritance and Modality Overrides

Extensions inherit the core schema. A modality extension may override interpretation of camera, lighting, color, materials, or composition when the modality has a more precise concept. For example, X-ray uses projection and density instead of photographic camera and light; MRI uses sequence, plane, and signal intensity; histopathology uses stain, magnification, cellular morphology, and slide preparation.

## 8. Diffusion Parameters

| Parameter | Description |
| --- | --- |
| `steps` | Number of denoising steps. Higher values may increase detail but also cost. |
| `cfg_scale` | Classifier-free guidance or equivalent prompt adherence parameter. |
| `sampler` | Sampling algorithm, such as Euler, DPM++, DDIM, or model-specific scheduler. |
| `seed` | Reproducibility seed when supported. |
| `denoise_strength` | Strength for image-to-image or refinement workflows. |

Adapters should preserve unsupported parameters as metadata rather than silently discarding them. If a model does not expose a parameter, the adapter should record `unsupported_by_target`.

## 9. Validation Framework

Validation combines structural checks, semantic checks, safety checks, and domain-specific checks.

### General Validation Checklist

| Check | Pass Criteria |
| --- | --- |
| Schema validity | Required fields are present and types are valid. |
| Intent clarity | The purpose of the image is explicit. |
| Prompt consistency | Prompt stacks do not contradict each other. |
| Output feasibility | Aspect ratio, quality, and render requirements are plausible. |
| Safety compliance | Safety constraints are present and enforceable. |
| Model portability | Unsupported fields are documented by the adapter. |

### Scoring System

| Score | Meaning |
| --- | --- |
| 0 | Fails critical requirements. |
| 1 | Major issues; generation should not proceed. |
| 2 | Usable only with warnings. |
| 3 | Acceptable. |
| 4 | Strong. |
| 5 | Excellent and reproducible. |

Generation should require:

- General imagery: total score >= 3 with no critical failures.
- Medical imagery: total score >= 4 with all safety checks passing.
- Radiography, MRI, and CT: anatomy, modality, and artifact checks must pass independently.

### Medical Validation

| Check | Applies To | Pass Criteria |
| --- | --- | --- |
| Anatomy plausibility | All modalities | Required structures appear in plausible orientation and proportion. |
| Pathology plausibility | Synthetic pathology | Pathology location, distribution, and severity are internally consistent. |
| Modality realism | All medical modalities | Visual signal matches the declared modality. |
| Radiography realism | X-ray | Projection, density, contrast, penetration, and view are plausible. |
| MRI realism | MRI | Plane, sequence, signal intensity, and artifacts are plausible. |
| CT realism | CT | Axial/coronal/sagittal anatomy, attenuation, kernel, and slice behavior are plausible. |
| Artifact realism | All modalities | Artifacts are plausible and not confused with pathology unless intentionally specified. |
| Safety check | All medical modalities | Synthetic-only, non-diagnostic, and no-real-patient-data rules pass. |

## 10. Medical Safety and Non-Diagnostic Use

All medical imagery governed by this specification must follow these rules:

- Medical images must be synthetic.
- Real patient data must never be used.
- Generated images must never imply a real diagnosis.
- Generated images must never be represented as real hospital studies.
- Generated images must never include fake clinical reports.
- Generated images must never include fake DICOM exports or metadata intended to pass as clinical records.
- Identity leakage must be avoided.
- Clinical misuse must be actively discouraged.
- Evidence falsification must be prevented.
- Educational and research labeling is strongly recommended.

Required labeling for medical examples:

```text
Synthetic image. Non-diagnostic. Not derived from real patient data. For education, research, simulation, or software testing only.
```

Implementations should reject requests that attempt to fabricate patient identity, hospital provenance, diagnostic findings, official reports, legal evidence, insurance evidence, or clinical trial evidence.

## 11. Multi-Model Compatibility

Create_images is model-agnostic but adapter-aware.

| Model Family | Adapter Guidance |
| --- | --- |
| SDXL | Use structured positive and negative prompts, seed, sampler, steps, CFG, and optional ControlNet metadata. |
| Flux | Convert weights to natural language or adapter metadata; preserve seed and resolution where available. |
| Midjourney | Convert fields to concise prompt text and parameter flags; record unsupported schema fields. |
| DALL-E | Convert weighted tokens to natural-language emphasis; preserve safety and output requirements in metadata. |
| Imagen | Use descriptive natural language with explicit composition and safety context. |

Adapters must report lossy conversions, unsupported parameters, and safety-relevant omissions.

## 12. Modality Extensions

Extensions should be versioned independently while declaring compatibility with core schema versions.

Recommended extension files:

- `extensions/radiology.md`
- `extensions/xray.md`
- `extensions/ct.md`
- `extensions/mri.md`
- `extensions/ultrasound.md`
- `extensions/histopathology.md`
- `extensions/oct.md`
- `extensions/pet.md`
- `extensions/dermoscopy.md`
- `extensions/endoscopy.md`
- `extensions/product_rendering.md`
- `extensions/technical_rendering.md`
- `extensions/education.md`

Each extension should define:

- Scope.
- Supported fields.
- Overrides.
- Validation rules.
- Examples.
- Safety requirements.
- Compatibility matrix.

## 13. Examples

### 13.1 Cinematic Portrait

```yaml
image_spec:
  version: "0.1.0"
  intent: cinematic
  model_family: sdxl
  output_type: image
  subject: "middle-aged engineer in a workshop"
  scene: "industrial workbench with precision tools"
  composition: "medium close-up, subject centered, shallow depth of field"
  camera:
    lens: "50mm"
    aperture: "f/2.8"
  lighting: "soft key light from left, subtle rim light"
  color_palette: ["steel gray", "warm amber", "muted teal"]
  style: ["cinematic realism", "natural skin texture"]
  mood: "focused and calm"
  environment: "clean fabrication studio"
  render_quality: "high detail, natural contrast"
  aspect_ratio: "16:9"
  diffusion:
    steps: 32
    cfg_scale: 6.5
    sampler: "DPM++ 2M Karras"
    seed: 14021
    denoise_strength: null
  prompt:
    core: "cinematic portrait of a middle-aged engineer in a clean fabrication studio"
    modifiers: ["precision tools", "realistic skin", "workshop atmosphere"]
    style_stack: ["cinematic realism"]
    quality_stack: ["high detail", "natural contrast"]
    composition_stack: ["medium close-up", "50mm lens", "shallow depth of field"]
    negative: "cartoon, plastic skin, distorted hands, unreadable tools"
  consistency_rules:
    - "hands and tools must be anatomically and mechanically plausible"
  safety_constraints:
    - "no real person identity"
  output_requirements:
    label: "Synthetic image"
```

Final prompt: cinematic portrait of a middle-aged engineer in a clean fabrication studio, precision tools, realistic skin, workshop atmosphere, cinematic realism, high detail, natural contrast, medium close-up, 50mm lens, shallow depth of field.

Negative prompt: cartoon, plastic skin, distorted hands, unreadable tools.

Reasoning: The image relies on photographic composition and controlled lighting while avoiding identity claims.

Expected output characteristics: realistic portrait, plausible tools, shallow depth of field, restrained cinematic color.

Safety considerations: synthetic person only; no claim of depicting a real individual.

### 13.2 Product Photography

```yaml
image_spec:
  version: "0.1.0"
  intent: product_render
  model_family: flux
  output_type: image
  subject:
    product: "matte black wireless headphones"
  scene: "studio tabletop"
  composition: "three-quarter product angle with clear silhouette"
  camera:
    lens: "85mm"
  lighting: "large softbox reflection, controlled highlight edges"
  color_palette: ["matte black", "cool gray", "white"]
  materials: ["soft-touch plastic", "brushed aluminum", "mesh fabric"]
  style: ["commercial product photography"]
  mood: "precise and premium"
  environment: "minimal studio"
  render_quality: "sharp edges, clean reflections"
  aspect_ratio: "1:1"
  diffusion:
    steps: 28
    cfg_scale: null
    sampler: null
    seed: 3007
    denoise_strength: null
  prompt:
    core: "studio product image of matte black wireless headphones"
    modifiers: ["three-quarter angle", "clean silhouette", "softbox reflections"]
    style_stack: ["commercial product photography"]
    quality_stack: ["sharp edges", "accurate materials"]
    composition_stack: ["centered", "square crop"]
    negative: "logo, text, warped earcups, extra cables, clutter"
  consistency_rules:
    - "left and right earcups must be symmetrical"
  safety_constraints:
    - "no trademarked logo"
  output_requirements:
    background: "plain"
```

Final prompt: studio product image of matte black wireless headphones, three-quarter angle, clean silhouette, softbox reflections, commercial product photography, sharp edges, accurate materials, centered square crop.

Negative prompt: logo, text, warped earcups, extra cables, clutter.

Reasoning: Materials, lighting, and composition define a reproducible product rendering target.

Expected output characteristics: clean product silhouette, plausible reflections, no brand marks.

Safety considerations: avoid trademark confusion and false advertising claims.

### 13.3 Sci-Fi Environment

```yaml
image_spec:
  version: "0.1.0"
  intent: environment_concept
  model_family: midjourney
  output_type: image
  subject: "orbital research habitat interior"
  scene: "hydroponic corridor overlooking a planet"
  composition: "wide establishing shot with strong leading lines"
  camera:
    lens: "24mm"
  lighting: "cool indirect habitat lighting with planet bounce light"
  color_palette: ["white", "green", "deep blue", "soft cyan"]
  materials: ["ceramic panels", "glass", "plant matter", "brushed metal"]
  style: ["hard science fiction", "functional design"]
  mood: "quiet, technical, inhabited"
  environment: "orbital station"
  render_quality: "high detail, plausible engineering"
  aspect_ratio: "16:9"
  diffusion:
    steps: null
    cfg_scale: null
    sampler: null
    seed: null
    denoise_strength: null
  prompt:
    core: "orbital research habitat interior, hydroponic corridor overlooking a planet"
    modifiers: ["functional engineering", "wide establishing shot", "leading lines"]
    style_stack: ["hard science fiction"]
    quality_stack: ["high detail", "plausible materials"]
    composition_stack: ["wide angle", "16:9"]
    negative: "fantasy magic, impossible machinery, cluttered unreadable design"
  consistency_rules:
    - "environment must remain mechanically plausible"
  safety_constraints:
    - "no real organization insignia"
  output_requirements:
    label: "Synthetic concept image"
```

Final prompt: orbital research habitat interior, hydroponic corridor overlooking a planet, functional engineering, wide establishing shot, leading lines, hard science fiction, high detail, plausible materials, wide angle, 16:9.

Negative prompt: fantasy magic, impossible machinery, cluttered unreadable design.

Reasoning: The concept uses environmental constraints and material plausibility to avoid generic fantasy imagery.

Expected output characteristics: readable station interior, plant systems, planet view, plausible scale.

Safety considerations: avoid implying affiliation with real agencies or spacecraft programs.

### 13.4 Synthetic Chest X-Ray

```yaml
image_spec:
  version: "0.1.0"
  intent: medical_synthetic
  model_family: sdxl
  output_type: radiograph
  subject: "synthetic adult chest radiograph"
  scene: "posterior-anterior chest X-ray view"
  composition: "upright PA chest framing, full lungs visible"
  camera: null
  lighting: null
  color_palette: ["grayscale"]
  materials: ["air", "soft tissue", "bone"]
  style: ["radiographic realism"]
  mood: null
  environment: "synthetic radiology education image"
  render_quality: "plausible grayscale density and anatomical contrast"
  aspect_ratio: "4:5"
  diffusion:
    steps: 36
    cfg_scale: 5.5
    sampler: "DPM++ 2M Karras"
    seed: 88211
    denoise_strength: null
  prompt:
    core: "synthetic non-diagnostic PA chest X-ray, adult thorax, full lungs visible"
    modifiers: ["radiographic grayscale", "plausible ribs and clavicles", "clear mediastinal silhouette"]
    style_stack: ["radiology education"]
    quality_stack: ["anatomically plausible", "realistic radiographic contrast"]
    composition_stack: ["upright PA view", "centered thorax"]
    negative: "real patient data, hospital labels, DICOM metadata, fake report, text overlays, impossible anatomy"
  consistency_rules:
    - "lung fields, ribs, clavicles, heart silhouette, and diaphragm must be spatially plausible"
  safety_constraints:
    - "synthetic only"
    - "non-diagnostic"
    - "no real patient data"
  output_requirements:
    label: "Synthetic image. Non-diagnostic. Not derived from real patient data."
medical_extension:
  enabled: true
  synthetic_only: true
  non_diagnostic: true
  modality: xray
  body_region: chest
  anatomy: ["lungs", "ribs", "clavicles", "heart silhouette", "diaphragm"]
  anatomical_plane: null
  projection_or_view: "PA"
  laterality: bilateral
  pathology:
    name: null
    presence: absent
    severity: null
    distribution: null
    diagnostic_features: []
  acquisition:
    protocol: "upright chest radiograph"
    contrast: "native radiographic contrast"
    slice_thickness: null
    reconstruction_kernel: null
    field_strength: null
    kvp: "typical chest radiography range"
    exposure: "adequate penetration"
    probe_type: null
    magnification: null
  visual_characteristics:
    density_pattern: "air dark, bone bright, soft tissue intermediate"
    signal_intensity: null
    echogenicity: null
    staining_pattern: null
    noise_profile: "mild detector noise"
    artifact_profile: "minimal motion"
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

Final prompt: synthetic non-diagnostic PA chest X-ray, adult thorax, full lungs visible, radiographic grayscale, plausible ribs and clavicles, clear mediastinal silhouette, radiology education, anatomically plausible, realistic radiographic contrast, upright PA view, centered thorax.

Negative prompt: real patient data, hospital labels, DICOM metadata, fake report, text overlays, impossible anatomy, patient identifiers, accession number, institution name.

Reasoning: The core schema defines the image target while the medical extension enforces modality, anatomy, acquisition, and safety constraints.

Expected output characteristics: grayscale radiograph-like synthetic image with plausible PA chest anatomy and no labels.

Safety considerations: synthetic-only, non-diagnostic, no real patient data, no fake DICOM or clinical report.

### 13.5 Synthetic Brain MRI

```yaml
image_spec:
  version: "0.1.0"
  intent: medical_synthetic
  model_family: flux
  output_type: image
  subject: "synthetic axial brain MRI slice"
  scene: "T2-weighted educational brain image"
  composition: "single centered axial slice"
  camera: null
  lighting: null
  color_palette: ["grayscale"]
  materials: ["gray matter", "white matter", "CSF", "skull"]
  style: ["MRI realism"]
  mood: null
  environment: "synthetic medical education"
  render_quality: "plausible signal intensity and anatomy"
  aspect_ratio: "1:1"
  diffusion:
    steps: 30
    cfg_scale: null
    sampler: null
    seed: 9174
    denoise_strength: null
  prompt:
    core: "synthetic non-diagnostic axial T2-weighted brain MRI slice"
    modifiers: ["centered brain anatomy", "CSF bright", "white matter darker than gray matter"]
    style_stack: ["MRI educational image"]
    quality_stack: ["plausible signal intensity", "clean anatomical boundaries"]
    composition_stack: ["axial plane", "single slice"]
    negative: "patient identifiers, scanner UI, fake DICOM, fake report, tumor claim, impossible symmetry"
  consistency_rules:
    - "signal intensity must match T2-weighted MRI conventions"
  safety_constraints:
    - "synthetic only"
    - "non-diagnostic"
    - "no real patient data"
  output_requirements:
    label: "Synthetic image. Non-diagnostic. Not derived from real patient data."
medical_extension:
  enabled: true
  synthetic_only: true
  non_diagnostic: true
  modality: mri
  body_region: brain
  anatomy: ["cerebral hemispheres", "ventricles", "gray matter", "white matter", "CSF"]
  anatomical_plane: axial
  projection_or_view: null
  laterality: bilateral
  pathology:
    name: null
    presence: absent
    severity: null
    distribution: null
    diagnostic_features: []
  acquisition:
    protocol: "T2-weighted brain MRI"
    contrast: "non-contrast"
    slice_thickness: "educational synthetic approximation"
    reconstruction_kernel: null
    field_strength: "not specified"
    kvp: null
    exposure: null
    probe_type: null
    magnification: null
  visual_characteristics:
    density_pattern: null
    signal_intensity: "CSF bright, white matter darker than gray matter"
    echogenicity: null
    staining_pattern: null
    noise_profile: "low synthetic acquisition noise"
    artifact_profile: "none or mild motion"
  clinical_constraints:
    anatomical_consistency: required
    pathology_consistency: required
    acquisition_plausibility: required
    no_patient_identity: required
    no_real_patient_data: required
  medical_negative_prompt: "patient identifiers, scanner console, DICOM tags, diagnostic report"
  validation:
    anatomy_check: required
    modality_check: required
    pathology_check: required
    artifact_check: required
    safety_check: required
```

Final prompt: synthetic non-diagnostic axial T2-weighted brain MRI slice, centered brain anatomy, CSF bright, white matter darker than gray matter, MRI educational image, plausible signal intensity, clean anatomical boundaries, axial plane, single slice.

Negative prompt: patient identifiers, scanner UI, fake DICOM, fake report, tumor claim, impossible symmetry, diagnostic report.

Reasoning: The example uses sequence-specific signal rules while avoiding diagnostic claims.

Expected output characteristics: square grayscale axial brain image with plausible T2 contrast.

Safety considerations: educational synthetic image only; no real scan metadata or diagnosis.

### 13.6 Synthetic Histopathology Slide

```yaml
image_spec:
  version: "0.1.0"
  intent: medical_synthetic
  model_family: imagen
  output_type: slide
  subject: "synthetic histopathology field"
  scene: "H&E stained lung tissue educational slide"
  composition: "microscopy field of view"
  camera:
    magnification: "20x equivalent"
  lighting: "brightfield microscopy simulation"
  color_palette: ["hematoxylin purple", "eosin pink", "white background"]
  materials: ["cell nuclei", "cytoplasm", "alveolar tissue"]
  style: ["histopathology realism"]
  mood: null
  environment: "synthetic pathology education"
  render_quality: "cellular detail with plausible staining variation"
  aspect_ratio: "4:3"
  diffusion:
    steps: null
    cfg_scale: null
    sampler: null
    seed: 62018
    denoise_strength: null
  prompt:
    core: "synthetic non-diagnostic H&E histopathology field of lung tissue"
    modifiers: ["brightfield microscopy", "plausible nuclei", "alveolar architecture"]
    style_stack: ["histopathology educational image"]
    quality_stack: ["cellular detail", "plausible stain variation"]
    composition_stack: ["20x field", "even illumination"]
    negative: "real patient data, slide label, accession number, fake diagnosis, impossible cells"
  consistency_rules:
    - "cellular structures and tissue architecture must be plausible for educational use"
  safety_constraints:
    - "synthetic only"
    - "non-diagnostic"
    - "no real patient data"
  output_requirements:
    label: "Synthetic image. Non-diagnostic. Not derived from real patient data."
medical_extension:
  enabled: true
  synthetic_only: true
  non_diagnostic: true
  modality: histopathology
  body_region: lung
  anatomy: ["alveolar tissue", "cell nuclei", "interstitium"]
  anatomical_plane: null
  projection_or_view: "brightfield microscopy"
  laterality: not_applicable
  pathology:
    name: null
    presence: absent
    severity: null
    distribution: null
    diagnostic_features: []
  acquisition:
    protocol: "H&E brightfield microscopy"
    contrast: "hematoxylin and eosin stain"
    slice_thickness: null
    reconstruction_kernel: null
    field_strength: null
    kvp: null
    exposure: null
    probe_type: null
    magnification: "20x"
  visual_characteristics:
    density_pattern: null
    signal_intensity: null
    echogenicity: null
    staining_pattern: "hematoxylin nuclei, eosin cytoplasm and stroma"
    noise_profile: "minimal scanner noise"
    artifact_profile: "mild stain variability"
  clinical_constraints:
    anatomical_consistency: required
    pathology_consistency: required
    acquisition_plausibility: required
    no_patient_identity: required
    no_real_patient_data: required
  medical_negative_prompt: "case number, patient label, diagnostic annotation, fake pathology report"
  validation:
    anatomy_check: required
    modality_check: required
    pathology_check: required
    artifact_check: required
    safety_check: required
```

Final prompt: synthetic non-diagnostic H&E histopathology field of lung tissue, brightfield microscopy, plausible nuclei, alveolar architecture, histopathology educational image, cellular detail, plausible stain variation, 20x field, even illumination.

Negative prompt: real patient data, slide label, accession number, fake diagnosis, impossible cells, case number, patient label, diagnostic annotation.

Reasoning: The prompt maps microscopy-specific acquisition and staining fields into the core visual framework.

Expected output characteristics: educational synthetic H&E-like field with plausible cell and tissue patterns.

Safety considerations: no patient label, no case number, no diagnostic annotation, no fake report.

## 14. Best Practices

- Keep core schema fields stable.
- Add specialized behavior through extensions.
- Use semantic versioning.
- Preserve unsupported fields as metadata.
- Label synthetic medical images clearly.
- Keep examples small and valid.
- Validate anatomy, modality, and safety before generation.
- Prefer explicit structured fields over ambiguous prose.
- Maintain adapter test cases for each model family.
- Keep commits small, incremental, and reviewable.

## 15. Failure Modes

| Failure Mode | Example | Required Response |
| --- | --- | --- |
| Schema drift | Field meaning changes silently | Add migration notes and version bump. |
| Prompt conflict | Cinematic lighting requested for X-ray | Apply modality override or reject. |
| Unsafe medical use | Fake report or DICOM requested | Reject. |
| Identity leakage | Real patient or person implied | Reject or anonymize to synthetic identity. |
| Modality mismatch | MRI prompt produces photographic brain | Fail modality validation. |
| Anatomy implausibility | Chest X-ray with impossible rib layout | Fail anatomy validation. |
| Pathology overclaim | Generated image described as diagnostic evidence | Fail safety validation. |
| Adapter loss | Target model lacks sampler support | Preserve metadata and warn. |

## 16. Repository Architecture

Recommended repository structure:

```text
Create_images/
├── README.md
├── Create_images.md
├── LICENSE
├── CHANGELOG.md
├── CONTRIBUTING.md
├── .gitattributes
├── schemas/
├── examples/
├── docs/
├── extensions/
│   ├── radiology.md
│   ├── xray.md
│   ├── ct.md
│   ├── mri.md
│   ├── ultrasound.md
│   ├── histopathology.md
│   └── oct.md
```

Recommended `.gitattributes`:

```gitattributes
*.md linguist-language=Markdown
*.yaml linguist-language=YAML
*.yml linguist-language=YAML
```

This improves GitHub language detection and repository readability by ensuring Markdown specifications, schema definitions, and YAML examples are classified correctly.

Recommended repository files:

- `README.md`
- `CONTRIBUTING.md`
- `LICENSE`
- `CHANGELOG.md`
- `docs/`
- `examples/`
- `schemas/`
- `extensions/`

## 17. Contribution Model

The project should use incremental open-source development practices:

- Use semantic versioning for public releases.
- Maintain a changelog for all user-visible changes.
- Prefer small commits and focused pull requests.
- Avoid monolithic rewrites of the specification.
- Keep schemas stable and versioned.
- Maintain extension compatibility matrices.
- Version modality specifications independently when needed.
- Provide migration notes for breaking changes.
- Require examples and validation notes for new fields.
- Discuss major changes through issues or design proposals before implementation.

### Schema Evolution

Compatible changes:

- Adding optional fields.
- Adding new extension files.
- Adding validation warnings.
- Adding examples.
- Clarifying documentation without changing semantics.

Breaking changes:

- Renaming required fields.
- Removing fields.
- Changing field meaning.
- Changing validation pass/fail criteria in incompatible ways.
- Changing safety semantics.

Breaking changes require a major version bump and migration guidance.

### Backwards Compatibility

Implementations should support at least the current minor version and the prior minor version of the core schema when practical. Extensions should declare compatible core versions explicitly.

## 18. Future Extensions

Future work may include:

- JSON Schema definitions.
- OpenAPI-compatible schema components.
- Model adapter conformance tests.
- ControlNet and image-to-image metadata.
- Video generation support.
- 3D and NeRF-aware rendering fields.
- Dataset card integration for synthetic datasets.
- Validation benchmark suites.
- Prompt linting tools.
- Medical modality packs for PET, dermoscopy, endoscopy, mammography, and fluoroscopy.
- Educational diagram and scientific visualization extensions.
- Signed metadata manifests for provenance and reproducibility.
