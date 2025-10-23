---
name: drupal-custom-context
description: This skill should be used when users need to create custom Behat Context classes to test Drupal configurations not covered by the default Drupal Spec Tool contexts, such as paragraph types, block types, webforms, Search API indexes, or other project-specific entities.
---

# Drupal Custom Context Generator

Create custom Behat Context classes that extend the Drupal Spec Tool to validate project-specific Drupal configurations.

## Purpose

Generate new Context classes following the Drupal Spec Tool's established patterns to test additional Drupal configuration types including:
- Custom block types
- Paragraph types and their fields
- Custom entity types
- View modes and form modes
- Text formats and filters
- Search API indexes
- Contact forms
- Webforms
- Custom module configuration

## When to Use This Skill

Use this skill when:
- The user needs to test Drupal entities not covered by default contexts
- Project-specific configuration requires validation
- The user mentions creating a "custom context" or testing specific entity types
- Standard contexts (ContentModel, Media, Views, Workflow, Menu, AccessControl) are insufficient

## Process

### 1. Understand Requirements

Ask the user:
- What Drupal configuration needs to be tested?
- What is the entity/config type (e.g., "paragraph type", "block type", "webform")?
- What properties should be validated (label, machine name, fields, settings)?

### 2. Analyze Existing Patterns

Reference existing context classes as templates:
- `src/Context/ContextBase.php` - Base class with utilities
- `src/Context/ContentModelContext.php` - Complex example with bundles and fields
- `src/Context/WorkflowContext.php` - Example with related entities
- `src/Context/ViewsContext.php` - Example with config entities

All custom contexts must:
- Extend `ContextBase`
- Use `EntityTypeManager` to query configuration
- Use `TableComparison` from `traviscarden/behat-table-comparison` for assertions
- Provide proper error labeling with `missingRowsLabelFor()` and `unexpectedRowsLabelFor()`

### 3. Generate Context Class

Create a new PHP class in `src/Context/` following this structure:

```php
<?php

namespace Acquia\DrupalSpecTool\Context;

use Behat\Gherkin\Node\TableNode;

/**
 * Provides context for testing [ENTITY_TYPE] configuration.
 */
class [NameContext] extends ContextBase {

  /**
   * Asserts that exactly the given [entities] exist.
   *
   * @Then exactly the following [entities] should exist
   */
  public function assert[Entities]Exist(TableNode $expected): void {
    // 1. Get entity type manager
    $entity_type_manager = $this->getEntityTypeManager();

    // 2. Load entities from Drupal
    $storage = $entity_type_manager->getStorage('[storage_type]');
    $entities = $storage->loadMultiple();

    // 3. Build actual data table
    $actual = [];
    foreach ($entities as $entity) {
      $actual[] = [
        'Name' => $entity->label(),
        'Machine name' => $entity->id(),
        // Add more properties as needed
      ];
    }

    // 4. Compare expected vs actual
    (new TableComparison($expected, $actual))
      ->expectHeader(['Name', 'Machine name'])
      ->ignoreRowOrder()
      ->setMissingRowsLabel($this->missingRowsLabelFor('[entities]'))
      ->setUnexpectedRowsLabel($this->unexpectedRowsLabelFor('[entities]'))
      ->assertEqual();
  }

}
```

### 4. Common Entity Types

#### Paragraph Types
- **Storage:** `paragraphs_type`
- **Properties:** Label, machine name, description
- **Fields:** Can reuse field validation from ContentModelContext

#### Block Types
- **Storage:** `block_content_type`
- **Properties:** Label, machine name, description

#### View Modes
- **Config:** `core.entity_view_mode.*`
- **Properties:** Entity type, view mode label, machine name

#### Form Modes
- **Config:** `core.entity_form_mode.*`
- **Properties:** Entity type, form mode label, machine name

#### Text Formats
- **Config:** `filter.format.*`
- **Properties:** Name, machine name, filters enabled

#### Search API Indexes
- **Config:** `search_api.index.*`
- **Properties:** Label, machine name, server, status, indexed items

#### Webforms
- **Config:** `webform.webform.*`
- **Properties:** Title, machine name, status, elements

### 5. Create Example Feature File

Generate a corresponding `.feature` file in `features/`:

```gherkin
Feature: [Entity Type Name]
  In order to ensure proper configuration
  As a developer
  I want to validate that [entities] are configured correctly

Scenario: [Entities] exist
  Then exactly the following [entities] should exist
    | Name        | Machine name | [Property] |
    | Example One | example_one  | value      |
```

### 6. Update behat.yml

Show how to register the new context:

```yaml
default:
  suites:
    default:
      contexts:
        # ... existing contexts ...
        - Acquia\DrupalSpecTool\Context\[NameContext]
```

## Quality Standards

Ensure all generated code:
- Follows PSR-12 coding standards
- Includes PHPDoc blocks for all methods
- Uses type hints for parameters and return types
- Handles edge cases (empty results, missing configuration)
- Provides clear error messages via TableComparison labels
- Validates that required columns are present

## Output

Provide:
1. **PHP Context Class** - Complete code in `src/Context/[NameContext].php`
2. **Example Feature File** - In `features/[name].feature`
3. **Documentation** - Comments explaining what it tests and how to use it
4. **Integration Instructions** - How to add to `behat.yml`
5. **Testing Guidance** - Example commands to run the tests

Begin by asking the user what Drupal configuration they need to test.
