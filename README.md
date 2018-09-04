# AIPs
ADAMANT Improvement Proposal (AIPs) repository describe standards for the ADAMANT Messenger platform, including core protocol specifications and client APIs.

# Contributing

 1. Review [AIP-1](AIPS/aip-1.md).
 2. Fork the repository by clicking "Fork" in the top right.
 3. Add your AIP to your fork of the repository. There is a [template AIP here](aip-X.md).
 4. Submit a Pull Request to ADAMANT's [AIPs repository](https://github.com/Adamant-im/AIPs).

Your first PR should be a first draft of the final AIP. It must meet the formatting criteria enforced by the build (largely, correct metadata in the header). An editor will manually review the first PR for a new AIP and assign it a number before merging it. Make sure you include a `discussions-to` header with the URL to a discussion forum or open GitHub issue where people can discuss the AIP as a whole.

If your AIP requires images, the image files should be included in a subdirectory of the `assets` folder for that AIP as follow: `assets/aip-X` (for aip **X**). When linking to an image in the AIP, use relative links such as `../assets/aip-X/image.png`.

When you believe your AIP is mature and ready to progress past the draft phase, you should open a PR changing the state of your AIP to 'Accepted'. An editor will review your draft and ask if anyone objects to its being accepted. After that implementation will start. And state will be change to 'Final' as soon as implementation will be done and pushed live
