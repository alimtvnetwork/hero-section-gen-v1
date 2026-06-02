# 37-codeowners.md — spec/01-rise-of-asia/01-homepage-hero-section
# Format: <path> <owner1> [<owner2> ...]
# A PR touching any path requires approval from at least one listed owner.

# Spec entry points + index
/spec/01-rise-of-asia/01-homepage-hero-section/00-index.md          @hero-owner @docs
/spec/01-rise-of-asia/01-homepage-hero-section/21-changelog.md         @hero-owner @docs
/spec/01-rise-of-asia/01-homepage-hero-section/19-archived.md          @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/01-conventions.md       @hero-owner @platform

# Design tokens, visual contract
/spec/01-rise-of-asia/01-homepage-hero-section/02-tokens.md            @design @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/13-spec.md              @design @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/05-hero-anatomy.md      @design @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/03-glossary.md          @design
/spec/01-rise-of-asia/01-homepage-hero-section/22-snapshots.md         @design @qa
/spec/01-rise-of-asia/01-homepage-hero-section/23-motion.md            @design @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/25-fonts.md             @design @perf

# A11y
/spec/01-rise-of-asia/01-homepage-hero-section/24-a11y.md              @a11y @hero-owner

# Data + component contract
/spec/01-rise-of-asia/01-homepage-hero-section/04-component-contract.md @hero-owner @platform
/spec/01-rise-of-asia/01-homepage-hero-section/09-edge-cases.md        @hero-owner @qa
/spec/01-rise-of-asia/01-homepage-hero-section/10-states.md            @hero-owner @copy
/spec/01-rise-of-asia/01-homepage-hero-section/11-storybook-fixtures.md @hero-owner @privacy

# i18n / copy
/spec/01-rise-of-asia/01-homepage-hero-section/07-i18n.md              @copy @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/COPY-27-budgets.md      @copy @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/38-copy-owners.md       @copy @legal

# Perf + runtime
/spec/01-rise-of-asia/01-homepage-hero-section/27-budgets.md           @perf @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/30-lazy-load.md         @perf @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/29-ssr-rules.md         @platform @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/31-cleanup.md           @platform @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/32-viewport-units.md    @design @platform
/spec/01-rise-of-asia/01-homepage-hero-section/12-threejs-fallback.md  @hero-owner @perf

# Security / supply chain
/spec/01-rise-of-asia/01-homepage-hero-section/28-csp.md               @security @platform
/spec/01-rise-of-asia/01-homepage-hero-section/35-secrets.md           @security @platform
/spec/01-rise-of-asia/01-homepage-hero-section/34-supply-chain.md      @security @platform
/spec/01-rise-of-asia/01-homepage-hero-section/39-dependencies.md      @security @platform
/spec/01-rise-of-asia/01-homepage-hero-section/36-source-maps.md       @security @platform

# Assets / CDN / portraits
/spec/01-rise-of-asia/01-homepage-hero-section/08-image-manifest.md    @design @platform-cdn
/spec/01-rise-of-asia/01-homepage-hero-section/33-cdn.md               @platform-cdn

# Ops / governance
/spec/01-rise-of-asia/01-homepage-hero-section/26-head-meta.md         @seo @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/41-seo.md               @seo
/spec/01-rise-of-asia/01-homepage-hero-section/42-cms-preview.md       @content @platform
/spec/01-rise-of-asia/01-homepage-hero-section/40-flags.md             @platform @hero-owner
/spec/01-rise-of-asia/01-homepage-hero-section/43-slo.md               @platform-sre
/spec/01-rise-of-asia/01-homepage-hero-section/44-retention.md         @privacy @platform
/spec/01-rise-of-asia/01-homepage-hero-section/45-priorities.md        @hero-owner @platform
/spec/01-rise-of-asia/01-homepage-hero-section/46-definition-of-done.md @hero-owner @qa @platform

# Portraits, fixtures, snapshots (binary)
/spec/01-rise-of-asia/01-homepage-hero-section/*.png                @design
/snapshots/hero/baseline/**             @design @qa
/src/components/hero/__fixtures__/**    @hero-owner @privacy