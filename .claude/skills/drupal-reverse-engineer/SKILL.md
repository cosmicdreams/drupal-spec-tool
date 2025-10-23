---
name: drupal-reverse-engineer
description: This skill should be used when users want to document an existing Drupal site by analyzing its configuration files and generating Drupal Spec Tool feature files. Particularly useful for legacy sites without documentation or creating baseline specifications for existing projects.
---

# Drupal Reverse Engineer

Analyze existing Drupal configuration and generate Drupal Spec Tool feature files that document the current state of a Drupal site.

## Purpose

Generate Gherkin feature files from existing Drupal YAML configuration files, enabling teams to:
- Document legacy Drupal sites that lack specifications
- Create baseline specifications for existing projects
- Validate that deployed configuration matches what's in source control

## When to Use This Skill

Use this skill when:
- The user wants to document an existing Drupal site
- Configuration files exist but feature files don't
- A baseline specification is needed for an established project
- The user mentions "reverse engineering" or "analyzing" Drupal configuration

## Process

### 1. Locate Configuration Directory

Ask the user where their Drupal config files are located. Common locations include:
- `config/sync/`
- `config/default/`
- `sites/default/config/`

If the directory doesn't exist, offer to search for alternative locations or analyze source code.

### 2. Analyze Configuration Files

Read YAML configuration files to extract Drupal entities:

**Content Types:** `node.type.*.yml` files
**Media Types:** `media.type.*.yml` files
**Taxonomies:** `taxonomy.vocabulary.*.yml` files
**Fields:** `field.field.*.yml` and `field.storage.*.yml` files
**Views:** `views.view.*.yml` files
**Image Styles:** `image.style.*.yml` files
**Workflows:** `workflows.workflow.*.yml` files
**Menus:** `system.menu.*.yml` files
**Roles:** `user.role.*.yml` files

### 3. Generate Feature Files

Create Gherkin feature files following the Drupal Spec Tool format. Reference the Context classes in `src/Context/` to ensure correct table column headers:

- `src/Context/ContentModelContext.php` - For bundles and fields format
- `src/Context/ViewsContext.php` - For views and displays format
- `src/Context/MediaContext.php` - For image styles and effects format
- `src/Context/WorkflowContext.php` - For workflows, states, transitions format
- `src/Context/MenuContext.php` - For menus format
- `src/Context/AccessControlContext.php` - For user roles format

#### Example: content_model.feature

```gherkin
Feature: Content model
  In order to enter structured content into my site
  As a content administrator
  I want to make sure the proper content structure is in place

Scenario: Bundles
  Then exactly the following content entity type bundles should exist
    | Name    | Machine name | Type         | Description |
    | Article | article      | Content type | News posts  |

Scenario: Fields
  Then exactly the following content entity type fields should exist
    | Bundle  | Field label | Machine name | Field type | Required | Cardinality | Form widget | Translatable |
    | article | Title       | title        | string     | Yes      | 1           | textfield   | Yes          |
```

#### Example: views.feature

```gherkin
Feature: Views

Scenario: Views
  Then exactly the following views should exist
    | Name     | Machine name | Base table | Status  | Description |
    | Articles | articles     | Content    | Enabled | Article list|

Scenario: View displays
  Then exactly the following views displays should exist
    | View     | Title | Machine name | Display plugin |
    | Articles | Page  | page_1       | Page           |
```

### 4. Data Extraction Guidelines

When parsing YAML files:
- Extract labels, machine names, descriptions
- For fields: capture type, required status, cardinality, widget settings, translatable status
- For views: capture base table, status, display configurations
- For workflows: extract states and transitions with from/to mappings
- Map internal values to human-readable format (e.g., `node` → `Content type`)
- Handle missing or optional configuration gracefully

### 5. Output

Create feature files in a `features/` directory with:
- Proper Gherkin syntax (Feature, Scenario, Then steps)
- Correct table formatting with `|` delimiters
- Helpful comments about what was analyzed

Provide a summary:
```
# Summary
Analyzed X configuration files and generated Y feature files:

- content_model.feature: Z bundles, W fields
- views.feature: V views, U displays
- media.feature: T image styles, S effects
- workflow.feature: R workflows, Q states, P transitions
- menus.feature: O menus
- access_control.feature: N roles

# Files Created
- features/content_model.feature
- features/views.feature
- features/media.feature
- features/workflow.feature
- features/menus.feature
- features/access_control.feature

Next steps:
1. Review generated feature files
2. Run `behat` to validate against the Drupal site
3. Adjust specification as needed
```

Begin by asking the user where their Drupal configuration files are located.
