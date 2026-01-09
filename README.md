# Houdini Skeletal VAT (Vertex Animation Textures)

This repository implements the Custom Skeleton Mode VAT pipeline. Unlike traditional vertex-based methods, this approach bakes bone matrices and calculates skinning at runtime to dramatically reduce the required texture size, while enabling shared animations across characters with identical skeletons.

> **Read the full post:** [3 Custom VAT Skeleton Modes: Efficient GPU Skinning](https://www.leiwu.co/posts/skeleton-vat-pipeline)

---

*For any settings or import procedures not specified below, please follow the standard SideFX Labs VAT Soft-Body Deformation mode.*

---

## Houdini Settings

* **Mode / Target Engine**: Select **Skeletal Animation (Bone)** and **Unreal Engine** (Currently only Unreal is supported).
* Assign the three outputs of your **fbxcharacterimport** to **Input Geometry**, **Rest Pose**, and **Anim Pose** respectively.
* **Texture Format**: Currently only **HDR** is supported.
* **Target Texture Width**: Set to `128`.
* **Export**:
  * **LBS Mode**: Requires **Position** and **Rotation** textures (Please mainly use LBS at the moment).
  * **DQS Mode**: Requires **Rotation** and **Color** textures.
  * **Matrix Mode**: Requires **Matrix** textures.

---

## Unreal Engine Settings & Import Guide

### I. FBX Import Settings

For simplicity, click the **Reset to Default** button at the top right of the **FBX Import Options** window, then modify the following settings:

| | |
| :--- | :--- |
| **Generate Missing Collision** | `Off` |
| **Vertex Color Import Option** | `Replace` |
| **Remove Degenerates** | `Off` |
| **Build Reversed Index Buffer** | `Off` |
| **Generate Lightmap UVs** | `Off` |
| **One Convex Hull Per UCX** | Depends on if FBX has been exported with custom UCX collisions |
| **Import Mesh LODs** | Depends on if Export LODs is turned on in Houdini on the VAT node |
| **Normal Import Method** | `Import Normals and Tangents` |
| **Material Import Method** | `Do Not Create Materials` |
| **Build Nanite** | `Off` |

---

### II. Texture Settings

| | |
| :--- | :--- |
| **Mip Gen Settings** | `NoMipmaps` |
| **Texture Group** | `16 Bit Data` |
| **Compression Settings** | `HDR(RGBA16F, no sRGB)` |
| **sRGB** | `Off` |

---

### III. Static Mesh Settings

* **Nanite Off**:
  * Uncheck **Enable Nanite Support** in the Static Mesh settings, then click **Apply Changes**.
* **Nanite On**:
  * 1. Check **Enable Nanite Support** in the Static Mesh settings, and (**Crucial**) uncheck **Lerp UVs**, then click **Apply Changes**.
  * 2. If characters explode or behave abnormally in certain areas when far from the camera, check **Renormalize Weights** and uncheck **Enable Bone 3** in the UE Material Instance (Currently, if Nanite is enabled, only 3 influences are supported, since the 4th index is stored in vertex alpha, which is mandatorily lerped by Nanite).
