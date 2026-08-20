---
title: "3D file formats explained: OBJ, FBX, glTF, STEP and USD"
source_url: "https://photolibsoftware.com/guides/3d-file-formats-explained/"
canonical: "https://photolibsoftware.com/guides/3d-file-formats-explained/"
language: "英文"
quality: "★★★"
section: "D. 数据交换格式 — 跨领域互操作性"
subsection: ""
retrieved: "2026-08-20"
status: "ok"
---

# 3D file formats explained: OBJ, FBX, glTF, STEP and USD

> 原始素材条目：3D File Formats Explained: OBJ, FBX, glTF, STEP, USD（PhotoLib）

> 原文链接：https://photolibsoftware.com/guides/3d-file-formats-explained/

> 摘要：OBJ vs FBX vs glTF vs STEP vs USD explained: which 3D formats your DAM can actually preview, which are opaque blobs, and how to pick an interchange target.

## 正文

Two families share the word “3D” and behave nothing alike. Mesh formats — OBJ, FBX, glTF, STL, USD — describe a surface as triangles, which is what artists, game engines and viewers use. CAD formats — STEP, IGES — store a boundary representation of exact curves and surfaces, so a hole is a mathematically perfect cylinder, not a ring of triangles.

## The short answer

Two families of format share the word “3D” but behave nothing alike. Mesh formats (OBJ, FBX, glTF, STL, USD) describe a surface as triangles — what artists, game engines and viewers use. CAD formats (STEP, IGES, and native parts like SolidWorks or Revit) describe precise mathematical solids for engineering. A catalog that can render one family may show a grey icon for the other, which is why 3D assets break so many general-purpose libraries. Get the interchange target right — glTF for meshes, STEP for CAD — and every downstream tool cooperates.

## The six formats at a glance

| FORMAT | WHAT IT STORES | BEST FOR | OPEN? | BROWSER PREVIEW |\n| --- | --- | --- | --- | --- |\n| OBJ (.obj) | Static geometry + basic materials (.mtl) | Handing off a single static mesh | Yes (open spec) | After conversion |
| FBX (.fbx) | Geometry, materials, rig, animation | Moving rigged/animated assets between DCC tools | No (Autodesk) | After conversion |
| glTF / GLB | Geometry, PBR materials, animation | Web, AR and viewer delivery — the interchange target | Yes (Khronos) | Native |
| USD / USDZ | Whole-scene composition & layers | Large film/sim pipelines; USDZ for Apple AR | Yes (Pixar/ASWF) | Partial / native (USDZ on Apple) |
| STL (.stl) | Raw triangle mesh, no color or units | 3D printing | Yes | After conversion |
| STEP (.stp/.step) | Precise CAD solids (B-rep), no textures | Neutral CAD interchange in engineering | Yes (ISO 10303) | After tessellation |

“After conversion/tessellation” means the format isn't drawn by a browser directly — the DAM must generate a mesh or image preview server-side. Only glTF/GLB (and USDZ on Apple hardware) render without that step.

## Mesh formats: OBJ, FBX, glTF, STL, USD

OBJ is the plain text of 3D — a decades-old Wavefront format that stores vertices, faces and, via a companion .mtl file, basic materials. No rig, no animation, often no single-file guarantee (textures live beside it). It is nearly universal precisely because it is simple, which makes it a safe way to hand a static prop from one tool to another.

FBX is the workhorse of production. Owned by Autodesk, it carries everything OBJ can't — skeletons, skinning, animation takes, cameras, lights — and is the default when an asset moves between 3ds Max, Maya, Blender, Unity and Unreal. The catch is that it's proprietary and versioned: an FBX exported from a newer tool can trip an older importer, so pipelines pin a version.

glTF (JSON-based) and its binary sibling GLB are the Khronos Group's answer to “a format built for delivery, not authoring.” Often called the JPEG of 3D, glTF is compact, supports physically-based materials and animation, and — crucially for a library — is rendered natively by browsers and most modern viewers. That is why it's the interchange target we recommend keeping alongside every master file, and why 3D asset management tools lean on it for previews.

USD (Universal Scene Description), from Pixar and now the Alliance for OpenUSD, is a different animal: it composes entire scenes from layered references rather than describing one object. It's the backbone of high-end film and simulation work; USDZ is Apple's zipped, single-file variant that iPhones and iPads open as AR out of the box. STL rounds out the set as the 3D-printing format — pure watertight geometry, no color, no units, so it's export-only for most catalogs.

## CAD formats: STEP, IGES and native parts

Engineering files are not meshes. A STEP file (.stp/.step, ISO 10303) stores a boundary representation — exact curves and surfaces — so a hole is a mathematically perfect cylinder, not a ring of triangles. That precision is the whole point in manufacturing, but it means a DAM can't just draw a STEP file; it has to tessellate it into a display mesh first. The same holds for older IGES (.igs) and for native parts like SolidWorks (.sldprt), Inventor (.ipt) and Revit (.rvt), which are proprietary on top of that. Keep STEP as the neutral interchange copy next to the native master, and confirm your specific extensions preview before you standardize — this is exactly the gap that separates a real engineering DAM from a generic one.

The opaque-blob trap: most tools will happily store any of these formats. Storing is not previewing. If the catalog shows a generic 3D icon instead of the actual model, nobody can find the right mesh without opening it — the single most common complaint we hear about 3D in a general DAM. Test preview, not just upload.

You’ll never get one format to do every job, so don’t try. The strategy that holds up: keep each asset in its original authoring format as the system of record — that’s where editability lives — and generate a lightweight glTF/GLB derivative for preview, web and AR. The master stays editable; the derivative does the showing.

## A format strategy that survives contact with reality

You will never get one format to do every job, so don't try. The pattern that holds up: keep originals in their authoring format as the system of record (that's where editability lives); generate a glTF/GLB derivative for preview, web and AR; and for engineering data, keep a STEP copy beside the native part as the neutral interchange. Then let a tool that understands the master-and-derivative relationship keep them in sync, so a change to the source regenerates the lightweight copies instead of leaving stale duplicates behind. The Khronos glTF standard (opens in new tab) has become the safe default derivative for exactly this reason.

Our top pick for mixed 3D + media archives: Daminion — on-premise, previews CAD and meshes beside photos and PDFs. Free trial, no credit card.

## FAQ

## Sources & references

- glTF — Khronos Group (opens in new tab) — glTF runtime format, accessed July 2026.

- OpenUSD (opens in new tab) — OpenUSD scene format, accessed July 2026.

- Daminion (opens in new tab) — DAM that catalogs 3D files, accessed July 2026.

- PhotoLib methodology — how we research and test DAM tools. See our methodology.

## Keep reading

## 图片

未识别到可下载的正文图片。

## 抓取记录

- 页面标题：3D file formats explained: OBJ, FBX, glTF, STEP and USD
- 抓取状态：ok
- 成功下载图片：0
- 图片失败：0
- 处理耗时：3.6 秒