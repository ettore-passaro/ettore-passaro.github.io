---
title: "Vuforia Engine Unity Extension"
excerpt: "From sample apps to primary maintainer of Vuforia's Unity integration, through a full architectural rewrite, across mobile and headwear platforms, and over a million developers."
header:
    teaser: "/assets/images/sdk_asset.webp"
categories:
  - Portfolio
tags:
  - Vuforia
  - Vuforia Unity Extension
  - Unity
  - SDK
  - AR
  - Augmented Reality

toc: true
toc_label: "Contents"
toc_sticky: true

classes: toc-vuforia
---

![Vuforia Engine Unity Extension](/assets/images/sdk_asset.webp)

## 1. Overview

[Vuforia](https://developer.vuforia.com) is one of the longest-running and most widely used platforms for building [Augmented Reality (AR)](https://en.wikipedia.org/wiki/Augmented_reality) applications. A 2021 industry analysis put its share of the mobile AR SDK market at **47%**, ahead of both ARKit and ARCore, and PTC has reported over **one million developers** using Vuforia Engine to date.

The [Vuforia Engine Unity Extension](https://developer.vuforia.com/library/getting-started/getting-started-vuforia-engine-unity) is the bridge between Vuforia's AR engine and [Unity](https://unity.com). It takes what Vuforia recognizes and tracks in the real world (images, physical objects, 3D-scanned spaces) and turns it into objects in a Unity scene that developers can configure and extend like any other part of a Unity project. Alongside it sits the [Vuforia Core Samples](https://assetstore.unity.com/packages/templates/packs/vuforia-core-samples-99026), a companion package of ready-to-use examples that has been the default starting point for new Vuforia projects since 2017. The Core Samples carry close to **5,000 favourites** on the Unity Asset Store, and the Extension has served thousands of monthly downloads across its release history.

I joined this project in November 2019 as the engineer responsible for the **public-facing layer** of the Extension: the sample apps, the release artifacts, and anything a developer would download, run, and learn from. Over six years that scope expanded until I had become the **primary maintainer** of the Extension, with ownership of new features, developer tools, platform support, and CI/CD infrastructure. When I left in September 2025, I had also taken on ownership of the Vuforia Creator App and contributed to the internal AR framework it is built on.

## 2. The Product

![Vuforia in the Unity Editor](/assets/images/vuforia-unity-printing-targets.webp)

In the Unity Editor, the Extension surfaces Vuforia's tracking capabilities as two complementary layers. The first is a **component layer**: pre-packaged assets (*prefabs*) and Editor-configurable objects that let developers add AR tracking to a Unity project without writing any code. The second is a [**C# scripting API**](https://developer.vuforia.com/references/unity/index.html) for developers who need custom logic beyond what the prefabs provide. Every Vuforia feature is available through both layers.

**Tracking targets** are the core of what the Extension exposes. Each target type corresponds to a different kind of real-world anchor: [Image Targets](https://developer.vuforia.com/library/vuforia-engine/images-and-objects/image-targets/image-targets/) recognize flat images; [Model Targets](https://developer.vuforia.com/library/vuforia-engine/images-and-objects/model-targets/model-targets/) recognize physical objects by 3D shape, designed for industrial workflows; [Area Targets](https://developer.vuforia.com/library/vuforia-engine/environments/area-targets/area-targets/) map entire 3D-scanned spaces for persistent factory-scale AR; and [Ground Plane](https://developer.vuforia.com/library/vuforia-engine/environments/ground-plane/ground-plane/) detects horizontal surfaces for placing content without a predefined target.

**Developer tools** extend Unity's own functionality: the Extension lets developers test AR features directly in the Editor without deploying to a device, using a webcam, a simulator, or pre-recorded AR sessions via the Session Recorder API.

{% include /links/dev-tools-video.html %}

## 3. Contributions

### The 10.0 Architectural Rewrite

Released in July 2021, Vuforia Engine 10.0 replaced the old Dataset/Tracker model, which required developers to manually manage active targets, with a new [Observer-based API](https://developer.vuforia.com/library/vuforia-engine/getting-started/vuforia-engine-api/vuforia-engine-10-api/) that handles lifecycle automatically. This required a complete rewrite of the native C++ layer, and on the Unity side meant rebuilding the component and prefab layer for every target type from the ground up.

{% include /diagrams/unity-extension-diagram.html %}

I ported Image Targets, Model Targets, and Object Targets to the new API, updated all the samples to match, and helped the documentation team write upgrade guides and code snippets — all while continuing to support and maintain the previous version of the Extension. The transition had to be **completely transparent to existing users**: the old API was kept alive throughout so developers could upgrade without touching their existing projects. Backwards compatibility is a standing expectation for every Vuforia release, and the 10.0 rewrite was no exception.

### Features and Performance

**New features.** Over the years I surfaced multiple native features in the Extension, including: [Session Recording](https://developer.vuforia.com/library/vuforia-engine/platform-support/recording-and-playback/recording-and-playback/), for which I designed the public interface and a default, customisable UI prefab for both mobile and eyewear devices; [Area Target Capture](https://developer.vuforia.com/library/vuforia-engine/environments/area-targets/capture-api/capture-api//area-target-capture-api-unity), built the same way; and [Mesh Observer](https://developer.vuforia.com/library/vuforia-engine/platform-support/rendering-in-native/mesh-observer), for rendering custom meshes with data provided by Vuforia.

**Simulator PlayMode.** The Play Mode simulator had been a persistent source of bug reports, spanning performance issues, race conditions, and even Editor crashes. The system used to work around the Extension's architecture to manually force status updates in ObserverBehaviours, completely bypassing (and working against) the observer pattern. Using existing functionality from the native Engine and the Extension, I replaced the system with one that would sit between the internal and public layers of the Extension, producing ad-hoc observations and feeding them to the observers as the Engine would do, effectively simulating a normal session of Vuforia. Following these changes, the flow of bug reports stopped.

**Area Targets.** Mesh-based scans suffered from long loading times when importing large scanned spaces into a Unity project. Point-cloud scans needed a custom rendering system for the scene view, which caused its own performance problems in the Editor. What looked like two separate issues turned out to share the same root cause: unexpected re-draws were restarting the import pipeline and redrawing the point cloud from scratch on every change in the scene view. Decoupling the two systems, adding an effective caching layer, and fixing the scene-view rendering logic resolved both issues, and bug reports for Area Targets came to a complete halt.

![Area Targets](/assets/images/area_targets.webp)

### Platform Support

**Unity versions.** The Extension generally has an LTS version of Unity as the minimum supported one, but new versions of Unity would often introduce breaking changes to the Extension and the samples, so guaranteeing support for them required building with non-LTS (and even beta) Unity builds and coordinating regression testing across multiple versions to catch issues before it reached developers. I advocated for reducing our workload and the constant need to upgrade Unity versions, resulting in dropping building with unstable beta builds and only focusing on stable versions that were more likely to be used by developers.

**Universal Render Pipeline (URP).** Supporting [URP](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@16.0/manual/index.html) meant making Vuforia fully compatible with projects using it, without breaking the many still on the older Built-in Render Pipeline. I added URP support by porting shaders and materials into per-pipeline settings files, with the Extension automatically selecting the right one for each project — completely transparent to developers.

**Mixed reality.** My first task on joining the project was completing and shipping the HoloLens and Magic Leap samples started by an engineer who had moved to a different team. I became the long-term owner of all the sample apps from there, growing them alongside each new Engine version, target type, and platform. Over time I maintained HoloLens 2 and Magic Leap 2 support through multiple SDK generations, ultimately unifying them under a single MRTK3 and OpenXR implementation.

![Vuforia Digital Eyewear Sample](/assets/images/digital_eyewear.webp)

### CI/CD and Release Infrastructure

The release process for the sample apps was entirely manual when I joined: slow, hard to hand off, and prone to mistakes caught only by human review at the last moment. I started automating it with Python scripts shortly after joining, progressively eliminating 90% of the manual work, and reducing the workload from a two-days two-people process down to a simple one-hour one-person task. A few years later I led the migration of the Extension's CI/CD pipelines to [GitHub Actions](https://github.com/features/actions), giving the team full ownership of their own workflows rather than depending on a centralised infrastructure team. I continued owning build optimisation from that point forward, and successfully reduced build times and artifact sizes by ~40%.

### Expanding Scope

In my final year I shipped features across three products simultaneously: the Unity Extension, the Vuforia Creator App, and the internal AR framework it is built on. These included [On-Prem Advanced Model Targets](https://developer.vuforia.com/library/vuforia-engine/images-and-objects/model-targets/advanced-model-targets/on-prem-advanced-model-targets/) and [On-Prem Step Check](https://developer.vuforia.com/library/vuforia-engine/step-check/step-check/), AI-powered model and procedure recognition from simple images; and [Apple Vision Pro](https://www.apple.com/apple-vision-pro/) hand-gesture support built on Unity's XR Interaction Toolkit, where I found a bug causing uncontrollable object manipulation, and fixed it by extending the toolkit and overriding the behaviour transferring hand rotation to objects. Following a full team replacement, I also trained an entirely new QA team from scratch on Vuforia Engine and Unity.

![Vuforia Step Check](/assets/images/vuforia-step-check.webp)

### Community

When QA coverage was limited, public forums became a primary channel for surfacing bugs. I engaged consistently with developers on [GitHub](https://github.com/PTCInc/vuforia-engine) and [Stack Overflow](https://stackoverflow.com/questions/tagged/vuforia), not as a formal responsibility but as a natural extension of owning the product's quality, and developers regularly noted the team's responsiveness during this period. I also worked directly with developers at Unity and other partner companies to track down and fix issues that affected both our products. One such collaboration resulted in the smooth integration of Vuforia with MRTK3 and OpenXR.

## 4. Conclusion

Working on the same product for six years taught me that **ownership is not just a title or a scope**, it is a habit of noticing. Spotting a problem before it becomes a bug report, knowing which team needs to be in the room for a given decision, asking QA not just whether something works but whether it is pleasant to use, looping in the documentation team to know how clear the purpose of a feature is. It also means being honest about capacity, with your team and with leadership, because a team that quietly takes on more than it can carry will always end up cutting corners, and the people paying for that are both the users and the developers themselves.

The other thing it made clear is that **if something feels painful to do repeatedly, it is worth stopping to ask why**. Sometimes the answer is automation, in which case investing time to simplify a tedious process pays back forever. Sometimes the answer is that the process should not exist at all. Either way, the instinct to question friction rather than just endure it is one of the more valuable things I took from this project.

The product is ultimately for people. End users tolerate missing features far better than **broken ones or unresponsive maintainers**. Reliability and trust are not nice-to-haves, they are what keeps a developer community alive.

Vuforia's Unity Extension has been used by over a million developers to build AR experiences around the world. I am proud to have been part of keeping that going.
