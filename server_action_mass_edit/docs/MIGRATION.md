# Migration to Version 19.0

## Overview

This document describes the migration of the `server_action_mass_edit` module from Odoo V18 to V19.

## Migration Date

February 2026

## Version Changes

- **Previous Version:** 18.0.1.1.1
- **New Version:** 19.0.1.0.0

## Changes Made

### 1. Module Version Bump

Updated `__manifest__.py` version from `18.0.1.1.1` to `19.0.1.0.0` following OCA versioning conventions.

### 2. Credits File Update

Removed past migration financing references from `readme/CREDITS.md` as per OCA migration guidelines.

### 3. Test Improvements

Added `tracking_disable` and `mail_notrack` context to the test class setup for:
- Improved test performance
- Avoid unnecessary mail tracking during tests
- Better compatibility with V19 testing best practices

```python
@classmethod
def setUpClass(cls):
    super().setUpClass()
    cls.env = cls.env(
        context=dict(cls.env.context, tracking_disable=True, mail_notrack=True)
    )
```

## Code Analysis Results

The following V19 migration items were reviewed and no changes were required:

### Items Already Compliant

| Item | Status | Notes |
|------|--------|-------|
| `groups_id` → `group_ids` | ✅ N/A | No `groups_id` references found in XML files |
| `self._cr/self._uid/self._context` | ✅ Compliant | Module already uses `self.env.context` |
| `odoo.osv.expression` → `odoo.fields.Domain` | ✅ N/A | No `osv.expression` usage found |
| `_sql_constraints` → `models.Constraint` | ✅ N/A | No SQL constraints defined in module |
| `read_group` → `_read_group` | ✅ N/A | No `read_group` calls found |
| `toggle_active` → `action_archive/action_unarchive` | ✅ N/A | Not used in module |
| `auto_join` → `bypass_search_access` | ✅ N/A | No `auto_join` parameter usage |
| `type="json"` → `type="jsonrpc"` | ✅ N/A | No controllers in module |
| Migration scripts removal | ✅ N/A | No `migrations/` folder existed |

## Dependencies

The module continues to depend on:
- `base` (Odoo core)
- `openupgradelib` (external Python dependency for pre_init_hook)

## Special Considerations

### Pre-Init Hook

The module includes a `pre_init_hook` that handles migration from the old `mass_editing` module:

- Renames model `mass.editing.line` → `ir.actions.server.mass.edit.line`
- Renames table `mass_editing_line` → `ir_actions_server_mass_edit_line`
- Merges module names from `mass_editing` → `server_action_mass_edit`

This hook uses `openupgradelib` and will execute during module installation if upgrading from an older module version.

### JavaScript Assets

The module includes JavaScript patches for the web client:
- `static/src/js/record.esm.js` - Patches for handling O2M field data
- `static/src/js/static_list.esm.js` - Patches for StaticList setup

These patches were reviewed and remain compatible with Odoo 19.0 OWL/web framework.

## Testing Recommendations

After migration, ensure to test:

1. **Mass editing wizard functionality**
   - Create a new mass edit server action
   - Add various field types (char, selection, many2many, one2many)
   - Execute mass editing on multiple records

2. **Field type handling**
   - Set/Remove operations for simple fields
   - Add/Remove/Set operations for many2many fields
   - Add/Set operations for one2many fields

3. **Demo data**
   - The module includes demo data for testing mass editing on users and partner titles
   - Note: Demo data is not installed by default in V19

## References

- [OCA Migration to V19 Guidelines](https://github.com/OCA/maintainer-tools/wiki/Migration-to-version-19.0)
- [Odoo 19.0 Coding Guidelines](https://www.odoo.com/documentation/19.0/contributing/development/coding_guidelines.html)
- [OCA Contributing Guidelines](https://odoo-community.org/page/contributing)
