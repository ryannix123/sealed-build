# The Sealed Build

**Nothing gets in after the doors close.**

An interactive explainer showing what hermetic (sealed) software builds are, why they matter, and how Red Hat's portfolio makes them practical for enterprise teams.

👉 **[Open the interactive demo](https://ryannix123.github.io/sealed-build/)**

---

## The problem

Most software supply chain attacks don't break in through the front door. They ride in on a dependency.

A typo-squatted npm package. A hijacked PyPI release. A container tag like `:latest` that quietly changed overnight. In a typical build, package managers reach out to the internet whenever they want, and whatever they pull down ends up in your product. Your lockfile and your SBOM say one thing. Your container may contain something else.

## The idea

A **hermetic build** flips the model:

1. **Declare** every input up front: exact versions, checksums, and base images pinned by digest.
2. **Prefetch** those inputs once, verify them, and record them.
3. **Seal** the build. The network is switched off. The compiler and package managers can only see what was declared.
4. **Prove** it. The build produces an SBOM and signed provenance that describe exactly what went in, because nothing else could get in.

The result is software you can explain. When an auditor asks what's in it, or a new CVE drops and leadership asks "are we exposed?", the answer is a query, not a war room.

## Why it matters

| Without a sealed build | With a sealed build |
|---|---|
| Any build step can fetch anything from the internet | Only declared, verified inputs are available |
| The SBOM describes what you *intended* to ship | The SBOM describes what you *actually* shipped |
| A poisoned dependency can slip in silently | An undeclared fetch fails the build loudly |
| Provenance is a best guess | Provenance is signed and verifiable |

Hermetic builds are a foundation for [SLSA](https://slsa.dev) Build Level 3 and for reproducible builds. Both increasingly show up in regulatory, audit, and customer security reviews.

## Why it's hard, and how Red Hat makes it easier

Red Hat builds its own products this way, in Konflux, with [Hermeto](https://github.com/hermetoproject/hermeto) handling hermetic prefetch. The same lessons and tooling are available to customers.

| Challenge | Red Hat answer |
|---|---|
| **Builds hide network calls.** Package managers fetch plugins, build tools, and binaries on their own. Turn off the network and builds break in undocumented ways. | **Red Hat Trusted Software Supply Chain.** Pipelines built on the same Konflux and Hermeto tooling Red Hat uses internally. Prefetch is handled for common ecosystems, and undeclared fetches fail loudly instead of slipping through. |
| **Developers drift from the pipeline.** Code that builds on a laptop with open internet access breaks in the sealed build. | **Red Hat OpenShift Dev Spaces.** Devfile-defined workspaces use the same base images and internal mirrors as the pipeline, so problems surface while coding, not at release time. |
| **Where do trusted inputs come from?** Sealing the build only helps if what you prefetched was safe. Pinning a vulnerable package just pins the risk. | **Red Hat Lightwell.** Lightwell Network supplies remediated, signed open source dependencies for Java and Python, with SBOMs, and backports fixes to the versions you already run. It becomes the trusted source your prefetch step pulls from. |
| **Proving it to auditors.** Evidence has to hold up under review, and policy has to block anything unproven. | **Red Hat Trusted Artifact Signer, Trusted Profile Analyzer, and Advanced Cluster Security.** Sign images and attestations, track SBOMs against new CVEs, and keep unsigned images off OpenShift. |

## The pipeline at a glance

```
 Inner loop        Sealed                       Prove and enforce
┌──────────┐   ┌──────────┐ ┌──────────────┐   ┌───────────────┐ ┌───────────────┐
│ Develop  │──▶│ Prefetch │▶│ Seal & build │──▶│ Attest & sign │▶│ Verify & run  │
│Dev Spaces│   │ Hermeto  │ │ network: OFF │   │ SBOM, SLSA,   │ │ Policy, ACS,  │
│ + Declare│   │+Lightwell│ │              │   │ Sigstore      │ │ OpenShift     │
└──────────┘   └──────────┘ └──────────────┘   └───────────────┘ └───────────────┘
```

| Stage | What happens | Red Hat piece |
|---|---|---|
| 1. Develop | Developers work in workspaces that match the pipeline | OpenShift Dev Spaces |
| 2. Declare | Lockfiles with checksums, base images by digest | Lockfiles + digests |
| 3. Prefetch | Inputs downloaded once, verified, and recorded | Hermeto, Lightwell Network |
| 4. Seal & build | The build runs with networking disabled | Konflux / OpenShift Pipelines |
| 5. Attest & sign | SBOM and signed provenance travel with the image | Tekton Chains, Trusted Artifact Signer |
| 6. Verify & run | Policy checks before deploy; SBOMs tracked against new CVEs | Conforma, Trusted Profile Analyzer, ACS, OpenShift |

## Using the demo

The page is a single self-contained `index.html` with no build step and no dependencies beyond Google Fonts.

- **Try it:** Toggle between **Open build** and **Sealed build**, then press **Run build** to watch a poisoned package either slip into the image or get blocked at the chamber wall.
- **The pipeline:** Click any of the six stages to see what happens, which Red Hat product does it, what the auditor gets, and the value it delivers.

To run it locally:

```bash
git clone https://github.com/ryannix123/sealed-build.git
open sealed-build/index.html
```

To host it on GitHub Pages: **Settings → Pages → Deploy from a branch → `main` / root**.

## Learn more

- [Red Hat blog: Zero CVEs is a symptom of a larger problem](https://www.redhat.com/en/blog/zero-cves-symptom-larger-problem)
- [IBM and Red Hat expand Lightwell](https://www.redhat.com/en/about/press-releases/ibm-and-red-hat-expand-lightwell-new-offerings-build-trust-infrastructure-ai-era-open-source)
- [Red Hat Lightwell](https://www.redhat.com/en/lightwell)
- [Konflux](https://konflux-ci.dev)
- [Hermeto](https://github.com/hermetoproject/hermeto)
- [SLSA: Supply-chain Levels for Software Artifacts](https://slsa.dev)

---

*This is a personal educational project and is not an official Red Hat publication. Product names and packaging reflect the Red Hat portfolio at the time of writing; check with your Red Hat account team for current details.*
