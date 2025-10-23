---
name: drupal-spec-builder
description: This skill should be used when users want to create Drupal Spec Tool feature files interactively through guided questions and natural language descriptions. Useful for teams starting new projects or those who prefer conversational interfaces over Google Sheet workflows.
---

# Drupal Specification Builder

Guide users through building comprehensive Drupal Spec Tool feature files via interactive questions and natural language processing.

## Purpose

Help users define their Drupal site architecture step-by-step and generate complete, valid Gherkin feature files. This makes the Drupal Spec Tool accessible to teams who find the Google Sheet workflow cumbersome or prefer conversational interfaces.

## When to Use This Skill

Use this skill when:
- Users want to create specifications interactively
- Starting a new Drupal project that needs architecture documentation
- Users prefer conversational interface over Google Sheets
- The user asks to "build" or "create" Drupal specifications

## Process

### 1. Introduction & Scope

Ask what aspects of the Drupal site should be documented:

```
What aspects of the Drupal site should be documented?

Options:
- Content Model (content types, media types, taxonomies, fields)
- Views (content listings and displays)
- Media (image styles and effects)
- Workflows (editorial states and transitions)
- Menus (navigation structure)
- Access Control (user roles)
- All of the above

Alternatively, describe the project and receive suggestions.
```

### 2. Content Model Builder

For content model specifications:

#### Bundles
Ask about content types, media types, and taxonomies. For each, gather:
- Name (e.g., "Article")
- Machine name (e.g., "article")
- Type (Content type, Media type, or Vocabulary)
- Description (optional)

Accept both structured lists and natural language descriptions like:
"We have Article and Event content types, and a Categories vocabulary"

#### Fields
For each bundle, gather field information:
- Field label (e.g., "Author")
- Machine name (e.g., "field_author")
- Field type (text, entity_reference, date, image, etc.)
- Required (Yes/No)
- Cardinality (1 for single, -1 for unlimited)
- Form widget (textfield, select, autocomplete, media_library, etc.)
- Translatable (Yes/No)
- Help text (optional)

Accept natural descriptions like:
"Article has a required multi-line body field, an optional author reference field with multiple values, and required categories using a select list"

### 3. Views Builder

For views specifications, gather:

**For each view:**
- Name (e.g., "Articles")
- Machine name (e.g., "articles")
- Base table (Content, Media, Users, Taxonomy terms, etc.)
- Status (Enabled/Disabled)
- Description (optional)

**For each display:**
- Display title (e.g., "Page")
- Machine name (e.g., "page_1")
- Display plugin (Page, Block, Feed, Attachment, etc.)

### 4. Media Builder

For image styles and effects:

**For each style:**
- Style name (e.g., "Large (480×480)")
- Machine name (e.g., "large")

**For each effect:**
- Effect name (Scale, Crop, Focal Point Scale and Crop, etc.)
- Summary/dimensions (e.g., "480×480")

### 5. Workflow Builder

For editorial workflows:

**Workflow:**
- Label (e.g., "Editorial")
- Machine name (e.g., "editorial")
- Type (usually "Content moderation")

**States:**
- State label (e.g., "Draft", "Published")
- Machine name

**Transitions:**
- Transition label (e.g., "Publish")
- Machine name (e.g., "publish")
- From state(s) - can be multiple
- To state - only one

### 6. Menus Builder

For each menu:
- Name (e.g., "Main navigation")
- Machine name (e.g., "main")
- Description (optional)

### 7. Access Control Builder

For each role:
- Role name (e.g., "Editor")
- Machine name (e.g., "editor")

Note: Anonymous and Authenticated are typically included by default.

## Smart Defaults & Suggestions

Provide helpful defaults:

- **Field Types:** If user says "text", ask whether they mean "string" (plain), "text" (formatted), or "text_with_summary" (body-style)
- **Widgets:** Suggest appropriate widgets based on field type
- **Cardinality:** Default to 1, suggest unlimited for taxonomy references
- **Translatable:** Default to Yes for content fields, No for references
- **Base Table:** Map common terms ("content" → "Content", "nodes" → "Content", "media" → "Media")

## Natural Language Processing

Accept flexible input formats:

**Structured:**
```
Article, article, Content type, News and blog posts
Event, event, Content type, Upcoming events
```

**Natural:**
```
We have three content types: Article for blog posts, Event for upcoming events, and Landing Page for custom layouts
```

**Conversational:**
```
User: Let's start with articles
Assistant: An "Article" content type with machine name "article"?
User: Yes
Assistant: What's it for?
User: Blog posts and news
```

## Validation & Feedback

Provide ongoing validation:
- ✓ Show progress: "Added 3 bundles, 12 fields so far"
- ⚠ Warn about issues: "Machine name 'Article Type' should be 'article_type' (lowercase, underscores)"
- 💡 Suggest improvements: "The field 'field_article_author' could be 'field_author' since it's scoped to the bundle"
- ✓ Confirm additions: "Added field_categories (Entity reference, unlimited, autocomplete) to article"

## Generate Feature Files

After gathering information:

1. **Validate Data**
   - Check machine names follow Drupal conventions (lowercase, underscores)
   - Ensure required fields are present
   - Validate field types are recognized Drupal types
   - Check for consistency (referenced bundles exist)

2. **Create Gherkin Files**

Generate properly formatted feature files following the Drupal Spec Tool format. Reference Context classes in `src/Context/` for correct table structures.

3. **Provide Summary**

```
## Summary

Created specifications for the Drupal site:

### content_model.feature
- 3 content types: Article, Event, Landing Page
- 2 media types: Image, Video
- 1 vocabulary: Categories
- 18 fields across all bundles

### views.feature
- 5 views with 12 total displays

### workflow.feature
- 1 workflow: Editorial
- 4 states: Draft, In Review, Published, Archived
- 8 transitions

## Next Steps

1. Install: `composer require --dev acquia/drupal-spec-tool`
2. Configure behat.yml with required contexts
3. Run tests: `behat`
4. Iterate as site evolves
```

## Interactive Refinement

After generating files, offer to:
1. Add more specifications to existing features
2. Modify existing specifications
3. Add new feature areas
4. Review specific validation rules
5. Export all files

## Error Recovery

When information is unclear:
- Ask clarifying questions
- Provide examples
- Offer to skip and return later
- Show what was understood and ask for confirmation

Begin by greeting the user and asking what they'd like to document.
