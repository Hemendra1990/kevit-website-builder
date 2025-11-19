# CLAUDE.md - AI Assistant Guide for Kevit Website Builder

## Project Overview

**Kevit Website Builder** is a browser-based, drag-and-drop website builder powered by Craft.js and React. The entire application is contained in a single HTML file that loads all dependencies via CDN/ESM, requiring no build process.

### Key Characteristics
- **Type**: Single-page application (SPA) - No framework/build tools
- **Architecture**: Monolithic HTML file with embedded JavaScript
- **Dependencies**: CDN-loaded (React, Craft.js, Tailwind CSS, Lucide Icons, Zod)
- **State Management**: React hooks + Craft.js editor state
- **Styling**: Tailwind CSS (CDN)
- **Language**: JavaScript (JSX via Babel transpiler)

## Repository Structure

```
kevit-website-builder/
├── .git/                    # Git repository
└── craftjs.html            # Main application file (complete builder in one file)
```

### File Inventory
- **craftjs.html** (66,823 bytes): Complete website builder application
  - Lines 1-68: HTML head with dependencies and configuration
  - Lines 69-944: Application code (React components + Craft.js integration)

## Technology Stack

### Core Technologies
1. **React 18.2.0** - UI library (from esm.sh)
2. **Craft.js 0.2.6** - Drag-and-drop page builder framework
3. **Tailwind CSS** - Utility-first CSS framework
4. **Babel Standalone** - JSX transformation in browser
5. **Lucide React** - Icon library
6. **Zod** - Schema validation for forms
7. **clsx** - Conditional className utility

### Module Loading
Uses import maps for clean ESM imports:
```javascript
"react": "https://esm.sh/react@18.2.0"
"react-dom/client": "https://esm.sh/react-dom@18.2.0/client"
"@craftjs/core": "https://esm.sh/@craftjs/core@0.2.6"
```

## Application Architecture

### Component Structure (Lines 73-944)

#### 1. Dynamic Data Components (Lines 87-211)
- **ListView**: Fetches and renders data from APIs
  - Props: `api`, `dataPath`, `layout`
  - Features: Template-based rendering, grid/list layouts
  - Dynamic data binding via dot notation (e.g., `user.address.city`)

#### 2. Core Components (Lines 214-276)
- **Text**: Editable text with font size control
- **Image**: Image component with URL and border radius
- **Heading**: H1-H4 headings with configurable tags

#### 3. Form Components (Lines 278-351)
- **FormContainer**: Form wrapper with Zod validation
- **FormInput**: Input fields (text/email/number) with validation
- **FormSubmit**: Submit button with styling options

#### 4. Layout & Advanced Components (Lines 353-738)
- **Container**: Flexible container with padding/background/shadow
- **Card**: Pre-styled card component
- **TwoColumns**: Responsive 2-column layout
- **Button**: Multi-variant button (contained/outlined/text)
- **Divider**: Horizontal rule
- **List**: Ordered/unordered lists
- **Link**: Anchor tags with dynamic URL support
- **Alert**: Info/success/warning/danger alerts
- **Badge**: Colored badges
- **ProgressBar**: Animated progress indicator
- **Header**: Navigation header with logo and menu
- **Menubar**: Horizontal navigation menu
- **Hero**: Hero section with customizable height/alignment
- **Tabs**: Tabbed content sections
- **Accordion**: Collapsible accordion panels
- **Skeleton**: Loading placeholders
- **Avatar**: User avatars with online status
- **Rating**: Star rating display
- **Timeline**: Vertical timeline component
- **Video**: YouTube video embed

#### 5. Editor UI (Lines 752-907)
- **RenderNode**: Component selection/deletion overlay
- **Toolbox**: Left sidebar with draggable components
- **SettingsPanel**: Right sidebar for component properties
- **TopBar**: Main toolbar with undo/redo, edit/preview toggle

### Key Features

#### Dynamic Data Binding
Components support dynamic data via `dynamicKey` prop:
```javascript
// Text component with dynamic binding
<Text text="Static" dynamicKey="user.name" />
// Renders: "John Doe" when user.name = "John Doe"
```

Supported on: Text, Heading, Image, Badge, Link, Avatar

#### API Integration (ListView)
```javascript
<ListView
  api="https://jsonplaceholder.typicode.com/users"
  dataPath="data.results"
  layout="grid grid-cols-2 gap-4"
/>
```

#### Form Validation
Uses Zod for runtime schema validation:
- Required fields
- Email validation
- Number coercion
- Custom error messages

## Development Workflows

### Running the Application
1. Open `craftjs.html` in a modern browser (Chrome, Firefox, Edge)
2. No build step required - all dependencies loaded via CDN
3. For local development: Use VS Code Live Server or `python -m http.server`

### Making Changes

#### Adding a New Component
1. Define the component function (lines 214-738 section)
2. Add settings component for configuration
3. Register in `userComponents` object (line 744)
4. Add to Toolbox (lines 778-843)

**Template:**
```javascript
const MyComponentSettings = () => {
  const { actions: { setProp }, props } = useNode((node) => ({ props: node.data.props }));
  return (
    <div className="space-y-3 p-1">
      <div>
        <label className="text-xs text-gray-500 block mb-1">Property</label>
        <input
          type="text"
          value={props.myProp}
          onChange={(e) => setProp(props => props.myProp = e.target.value)}
          className="w-full px-2 py-1 border rounded text-sm"
        />
      </div>
    </div>
  );
};

const MyComponent = ({ myProp }) => {
  const { connectors: { connect, drag } } = useNode();
  return (
    <div ref={ref => connect(drag(ref))} className="p-4">
      {myProp}
    </div>
  );
};

MyComponent.craft = {
  displayName: 'My Component',
  props: { myProp: 'default value' },
  related: { settings: MyComponentSettings }
};
```

#### Modifying Styles
- Global styles: Lines 44-67 (CSS block)
- Tailwind config: Lines 11-24 (custom colors, animations)
- Component styles: Inline Tailwind classes

#### Changing Dependencies
Update import map (lines 31-42):
```javascript
"react": "https://esm.sh/react@18.2.0",  // Change version here
```

### Testing Workflow
1. Open browser DevTools (F12)
2. Check Console for errors
3. Test drag-and-drop functionality
4. Toggle Edit/Preview modes
5. Test form validation
6. Test API integration with ListView

### Debugging Tips
- **Component not rendering**: Check `userComponents` registration (line 744)
- **Settings not appearing**: Verify `craft.related.settings` property
- **Drag-and-drop issues**: Check `connect(drag(ref))` in component
- **API errors**: Open Network tab, check CORS and endpoint
- **Styling issues**: Verify Tailwind classes or check custom CSS

## Code Conventions

### Component Patterns
1. **Craft.js Integration**: All components use `useNode()` hook
2. **Settings Components**: Named as `{ComponentName}Settings`
3. **Props Management**: Use `setProp()` for updates
4. **Refs**: Always use `ref => connect(drag(ref))`

### Naming Conventions
- Components: PascalCase (`MyComponent`)
- Props: camelCase (`myProp`)
- CSS classes: Tailwind utility classes
- IDs: Generated by Craft.js (don't hardcode)

### State Management
- **Editor State**: Managed by Craft.js `<Editor>` component
- **Component State**: React hooks (`useState`, `useEffect`)
- **Form State**: Managed by browser FormData API
- **API State**: Local component state with `useState`

### Code Organization
```
Lines 1-68:     HTML head & dependencies
Lines 73-86:    Imports & helper functions
Lines 87-211:   Dynamic data components
Lines 214-276:  Core components
Lines 278-351:  Form components
Lines 353-738:  Layout & advanced components
Lines 744-750:  Component registration
Lines 752-776:  Render node wrapper
Lines 778-845:  Toolbox (component palette)
Lines 848-881:  Settings panel
Lines 884-907:  Top toolbar
Lines 909-944:  App component & render
```

## Common Tasks for AI Assistants

### 1. Adding a New Component Type
**Steps:**
1. Create component function with `useNode()` hook
2. Create settings component
3. Add `craft` property with defaults
4. Register in `userComponents` (line 744)
5. Add ToolItem in Toolbox (lines 790-843)

### 2. Modifying Existing Component
**Location:** Find component by name (Ctrl+F)
**What to change:**
- Component logic: Main component function
- Settings: `{ComponentName}Settings` function
- Defaults: `craft.props` object

### 3. Styling Changes
**Global styles:** Lines 44-67
**Tailwind config:** Lines 11-24
**Component styles:** Inline Tailwind classes in JSX

### 4. Adding Dependencies
**Update import map:** Lines 31-42
```javascript
"my-lib": "https://esm.sh/my-lib@1.0.0"
```

### 5. Debugging Issues
**Check:**
1. Browser console for errors
2. Component registration in `userComponents`
3. Craft.js hooks (`useNode`, `useEditor`)
4. Tailwind class names (no typos)
5. CORS issues for API calls

## Important Notes for AI Assistants

### DO:
- Maintain single-file architecture (everything in craftjs.html)
- Use Tailwind utility classes for styling
- Follow existing component patterns
- Test in browser after changes
- Keep import map URLs to esm.sh
- Use semantic HTML

### DON'T:
- Split into multiple files (defeats single-file purpose)
- Add build tools (Webpack, Vite, etc.)
- Use CSS-in-JS libraries (conflicts with Tailwind)
- Hardcode component IDs (Craft.js generates them)
- Use class components (only functional components)
- Add npm dependencies (use CDN only)

### Security Considerations
- **XSS**: User input is not sanitized - add validation if needed
- **CORS**: API calls may fail due to CORS policy
- **CSP**: Inline scripts may be blocked by strict CSP
- **Validation**: Forms use Zod but server-side validation still needed

### Performance Tips
- ListView fetches API data only in preview mode
- Components use React.memo where beneficial
- Craft.js handles undo/redo efficiently
- Large component trees may slow down editor

## Git Workflow

### Current State
- Branch: `claude/claude-md-mi66wlugsk947bmi-01FKJZcQSD8G4nDM4rJ9KWVA`
- Last commit: `65c1ee8 Initial Commit`
- Files tracked: 1 (craftjs.html)

### Making Changes
1. Edit `craftjs.html`
2. Test in browser
3. Commit with descriptive message
4. Push to feature branch

### Commit Message Guidelines
```
feat: Add new ComponentName component
fix: Resolve drag-and-drop issue in Container
style: Update Tailwind configuration
refactor: Simplify FormInput validation logic
docs: Update component documentation
```

## API Reference

### Craft.js Hooks

#### useNode()
```javascript
const {
  connectors: { connect, drag },
  actions: { setProp },
  props,
  isActive,
  isHover
} = useNode((node) => ({
  props: node.data.props,
  isActive: node.events.selected,
  isHover: node.events.hovered
}));
```

#### useEditor()
```javascript
const {
  actions,
  query,
  enabled,
  canUndo,
  canRedo
} = useEditor((state, query) => ({
  enabled: state.options.enabled,
  canUndo: query.history.canUndo(),
  canRedo: query.history.canRedo()
}));
```

### Helper Functions

#### get(obj, path, defaultValue)
Resolves dot notation paths in objects:
```javascript
get({ user: { name: 'John' } }, 'user.name', 'N/A')
// Returns: 'John'
```

## Troubleshooting Guide

### Issue: Component not draggable
**Solution:** Ensure `ref => connect(drag(ref))` is applied to root element

### Issue: Settings panel empty
**Solution:** Check `craft.related.settings` property exists

### Issue: API not loading in ListView
**Solution:**
1. Check CORS policy
2. Verify `dataPath` points to array
3. Open Network tab to inspect response

### Issue: Tailwind classes not working
**Solution:**
1. Check for typos in class names
2. Verify Tailwind CDN loaded (line 9)
3. Check browser DevTools for CSS conflicts

### Issue: Form validation not working
**Solution:**
1. Verify Zod imported (line 38)
2. Check field `name` attributes
3. Ensure `required` prop set correctly

## Future Enhancement Ideas

### Potential Features
1. **Export to HTML**: Serialize final page to standalone HTML
2. **Component Templates**: Pre-built page templates
3. **Asset Manager**: Image upload and management
4. **Responsive Preview**: Mobile/tablet/desktop views
5. **Custom Code Injection**: Allow custom CSS/JS
6. **Version History**: Save/load multiple versions
7. **Component Library**: Import external components
8. **Theme System**: Color palette management
9. **SEO Settings**: Meta tags and structured data
10. **Collaboration**: Multi-user editing

### Technical Improvements
1. Add TypeScript definitions
2. Implement component search in Toolbox
3. Add keyboard shortcuts
4. Improve accessibility (ARIA labels)
5. Add component documentation tooltips
6. Implement auto-save functionality
7. Add error boundaries
8. Optimize re-renders with React.memo
9. Add unit tests (Jest + React Testing Library)
10. Implement analytics tracking

## Resources & Documentation

### External Documentation
- **Craft.js**: https://craft.js.org/docs/overview
- **React**: https://react.dev/
- **Tailwind CSS**: https://tailwindcss.com/docs
- **Lucide Icons**: https://lucide.dev/
- **Zod**: https://zod.dev/

### Key Files to Reference
- `craftjs.html:744-750` - Component registration
- `craftjs.html:778-843` - Toolbox configuration
- `craftjs.html:31-42` - Dependency versions

### Community & Support
- GitHub Issues: Use for bug reports
- Feature Requests: Document in issues
- Questions: Check Craft.js Discord/GitHub discussions

---

**Last Updated:** 2025-11-19
**Version:** 1.0.0
**Maintainer:** AI Assistant

*This document is intended for AI assistants working with this codebase. Keep it updated as the project evolves.*
