# Atomic Design Refactoring Plan

## Scope

Establish a practical Atomic Design foundation across the Next.js web app and React Native mobile app without forcing the two platforms into identical rendering implementations.

The current applications are starter screens. This plan introduces reusable boundaries and tokens while preserving the current behavior until real product features are added.

## Audit Summary

### Web app

- `app/page.tsx` contains the complete page markup, layout, typography, links, and button styling in one route component.
- The route has repeated link/button patterns with no reusable atoms or molecules.
- Tailwind utility values are applied directly throughout the page; there is no project-owned token layer beyond the generated CSS variables.
- `app/layout.tsx` owns document metadata and shell setup appropriately, but there is no reusable template or page composition boundary yet.
- The page remains the default Next.js starter content, so there are no domain organisms or feature workflows to preserve.

### Mobile app

- `mobile/App.tsx` owns the application shell, safe-area handling, status bar behavior, and screen composition.
- The visible screen is delegated to `NewAppScreen`, so the app has no project-owned atoms, molecules, organisms, templates, or screens.
- `mobile/__tests__/App.test.tsx` only verifies that the root component can be rendered; it does not verify visible content, accessibility, or interaction behavior.
- Native safe-area and status-bar behavior are already appropriate shell responsibilities and should remain outside low-level visual atoms.

### Shared foundation

- There are currently no `components/`, `mobile/components/`, token, theme, or shared package directories.
- The web and native apps have separate dependency and build pipelines.
- No cross-platform component abstraction should be introduced until there is a real repeated concept used by both platforms.

## Target Architecture

Use platform-specific component trees with shared naming and design semantics:

```text
components/
  atoms/
  molecules/
  organisms/
  templates/
  pages/

mobile/
  components/
    atoms/
    molecules/
    organisms/
    templates/
  screens/
```

The web `pages/` layer represents route-level page composition. The native `screens/` directory represents app-level screens. Do not force a web route component and a native screen to share a renderer when their accessibility or platform behavior differs.

## Atomic Design Rules

- Atoms own one primitive visual or interaction responsibility.
- Molecules compose atoms into one cohesive task.
- Organisms compose meaningful sections and may coordinate local UI state.
- Templates define layout and slots but should not own feature-specific content.
- Pages and screens connect real content, navigation, data, and feature state to templates.
- Lower layers must not import pages, route modules, or feature-specific organisms.
- Domain data fetching and navigation orchestration stay above organisms.
- Components must expose semantic states such as disabled, loading, error, empty, focused, and pressed where relevant.
- Web components use semantic HTML and keyboard/focus behavior. Native components use native controls, safe areas, touch targets, and platform conventions.
- Prefer project tokens for color, spacing, typography, radii, borders, and elevation instead of one-off values.

## Implementation Phases

### Phase 1: Establish tokens and conventions

1. Define a small web token layer for colors, typography, spacing, radii, borders, and focus states in the existing CSS/Tailwind setup.
2. Define an equivalent native token module using platform-neutral names and React Native-compatible values.
3. Document naming and import direction conventions in the component directories.
4. Add path aliases only if they reduce imports without obscuring ownership.

**Exit criteria:** tokens compile in both apps, have matching semantic names where concepts are shared, and no component imports from a higher Atomic Design layer.

### Phase 2: Extract web primitives

1. Extract a web `Text` or typography primitive only if it reduces repeated typography decisions.
2. Extract a web `Link`/action primitive that preserves external-link behavior, focus states, and target attributes.
3. Extract an `Image`/brand mark primitive only if the logo is used in more than one place.
4. Refactor `app/page.tsx` to compose these atoms without changing the starter screen’s visible behavior.

**Exit criteria:** the route contains composition rather than repeated primitive styling, and lint/build pass.

### Phase 3: Extract web compositions

1. Create a molecule for the starter content block containing the heading, explanatory text, and inline links if that composition is reused or independently testable.
2. Create a molecule or organism for the action group containing the deployment and documentation actions.
3. Introduce a page template only when a stable shell or repeated page layout exists.
4. Keep `app/layout.tsx` responsible for document metadata and global shell concerns.

**Exit criteria:** page-level code expresses content and composition; reusable sections have focused tests; no artificial wrapper exists solely to satisfy a layer name.

### Phase 4: Replace the native starter screen

1. Replace `NewAppScreen` with a project-owned native screen so the application owns its UI.
2. Keep `SafeAreaProvider`, `StatusBar`, and safe-area handling at the app shell/template boundary.
3. Introduce native atoms only for primitives needed by the new screen, such as text, button, logo, or surface.
4. Build native molecules for grouped content and actions only when those groups have a cohesive purpose.
5. Preserve accessible labels, native press states, minimum touch targets, and light/dark appearance behavior.

**Exit criteria:** `App.tsx` composes a project-owned screen, the starter dependency is no longer required for the visible UI, and the app renders on iOS and Android.

### Phase 5: Cross-platform consistency and validation

1. Compare web and native semantic tokens and interaction states, not raw CSS and React Native style values.
2. Identify concepts that are genuinely shared. Keep shared data/types separate from platform renderers unless a shared renderer is demonstrably beneficial.
3. Add focused web and native tests for visible content and primary interactions.
4. Run web lint/build and mobile lint/tests; run iOS and Android smoke launches when simulators/emulators are available.
5. Record intentional exceptions, especially platform-specific navigation, safe-area, keyboard, focus, and accessibility behavior.

**Exit criteria:** both applications have an understandable layer map, shared concepts are consistent, platform differences are explicit, and validation evidence is recorded.

## Validation Matrix

| Surface | Check |
| --- | --- |
| Web types and lint | `npm run lint` |
| Web production build | `npm run build` |
| Native lint | `cd mobile && npm run lint` |
| Native unit/component tests | `cd mobile && npm test` |
| iOS smoke test | `cd mobile && npm run ios` with an available simulator |
| Android smoke test | `cd mobile && npm run android` with an available emulator |
| Accessibility | Keyboard/focus checks on web; labels, touch targets, and screen-reader semantics on native |
| Responsive behavior | Web desktop and mobile viewports; native portrait and supported orientations |

## Risks and Decisions

- The apps are currently too small to justify a complete shared UI package. Reassess after the first real feature introduces repeated concepts.
- Atomic Design is a responsibility model, not a requirement to create five directories for every feature.
- Moving files during the initial foundation phase can create churn without product value. Prefer extracting stable primitives in place and move only when ownership becomes unclear.
- The existing iOS scene lifecycle fix and Android build configuration are infrastructure concerns; they should not be moved into UI component layers.
- Any future design system library must be evaluated against the existing Next.js, Tailwind, React Native, Metro, and native build constraints before adoption.

## Definition Of Done

- Web and native component ownership is documented and follows the layer direction rules.
- Reusable primitives use semantic tokens and expose required interaction states.
- Route-level and screen-level components focus on composition, content, and state.
- No starter-only UI dependency remains where project-owned UI is required.
- Web and mobile validation commands pass, or environment blockers are explicitly documented.
- Intentional platform differences and remaining exceptions are listed in the implementation report.
