# AIPs

ADAMANT Improvement Proposal (AIPs) repository describe standards for the ADAMANT Messenger platform, including core protocol specifications and client APIs.

## Site build

This repository is built as a Jekyll site.

- Ruby gems are managed with Bundler via [Gemfile](Gemfile)
- The site currently builds with Jekyll 4.x and the `minima` theme
- Local build output is generated into [_site](_site)

### Local setup

Install dependencies:

```sh
bundle install
```

Run the local development server:

```sh
bundle exec jekyll serve
```

Build the site without serving it:

```sh
bundle exec jekyll build
```

If you change [_config.yml](_config.yml), restart `jekyll serve`, because Jekyll does not reload that file automatically.

In VS Code, this workspace disables built-in SCSS validation because Jekyll entrypoint stylesheets with front matter can trigger false parser errors even when `bundle exec jekyll build` succeeds.

### Deployment

The published site is deployed to GitHub Pages for the [Adamant-im/AIPs](https://github.com/Adamant-im/AIPs) repository and served on the custom domain `https://aips.adamant.im` configured in [CNAME](CNAME).

Generated AIP pages currently resolve under paths such as `https://aips.adamant.im/AIPS/aip-21`. When linking to AIP pages from Jekyll templates, prefer each page's generated `page.url` instead of hardcoding `/aip-N` paths.

GitHub Pages is configured to publish from the `master` branch. The settings page itself is not publicly readable without repository access, so this README documents the verified public deployment target, branch, and local build process.

## Contributing

 1. Review [AIP-1](AIPS/aip-1.md).
 2. Fork the repository by clicking "Fork" in the top right.
 3. Add your AIP to your fork of the repository. There is a [template AIP here](aip-X.md).
 4. Submit a Pull Request to ADAMANT's [AIPs repository](https://github.com/Adamant-im/AIPs).

Your first PR should be a first draft of the final AIP. It must meet the formatting criteria enforced by the build (largely, correct metadata in the header). An editor will manually review the first PR for a new AIP and assign it a number before merging it. Make sure you include a `discussions-to` header with the URL to a discussion forum or open GitHub issue where people can discuss the AIP as a whole.

If your AIP requires images, the image files should be included in a subdirectory of the `assets` folder for that AIP as follow: `assets/aip-X` (for aip **X**). When linking to an image in the AIP, use relative links such as `../assets/aip-X/image.png`.

When you believe your AIP is mature and ready to progress past the draft phase, you should open a PR changing the state of your AIP to 'Accepted'. An editor will review your draft and ask if anyone objects to its being accepted. After that implementation will start. And state will be change to 'Final' as soon as implementation will be done and pushed live
