---
title: Access Control
aliases:
    - /guides/rbac
    - /account_management/rbac/role_api
    - /account_management/users/default_roles
    - /account_management/users/custom_roles
    - /account_management/rbac/log_management
further_reading:
    - link: '/api/v2/roles/'
      tag: 'Documentation'
      text: 'Manage roles and permissions with the Roles API'
    - link: '/api/v2/roles/#list-permissions'
      tag: 'Documentation'
      text: 'Manage your permissions with the Permissions API'
    - link: '/account_management/rbac/permissions'
      tag: 'Documentation'
      text: 'Discover the list of permissions available'
    - link: '/account_management/saml/'
      tag: 'Documentation'
      text: 'Enable single sign on with SAML'
    - link: "https://www.datadoghq.com/blog/compliance-governance-transparency-with-datadog-audit-trail/"
      tag: "Blog"
      text: "Build compliance, governance, and transparency across your teams with Datadog Audit Trail"
algolia:
  tags: ["rbac"]
---

## Overview

Datadog offers a flexible access management system that allows you to customize the level at which you control access to your Datadog resources.

Users looking for basic functionality have access to OOTB [roles](#role-based-access-control) with [permissions][1]. For more flexibility, create your own [custom roles](#custom-roles) to combine permissions into new roles. Permissions attached to a custom role apply to all resources of a particular resource type.

Organizations and users that need maximum flexibility can control access to individual dashboards, notebooks, and other resources with [granular access control][2].

## Role based access control

Roles categorize users and define what account permissions those users have, such as what data they can read or what account assets they can modify. By default, Datadog offers three roles, and you can create [custom roles](#custom-roles) so you can define a better mapping between your users and their permissions.

By granting permissions to roles, any user who is associated with that role receives that permission. When users are associated with multiple roles, they receive all the permissions granted to each of their roles. The more roles a user is associated with, the more access they have within a Datadog account.

If a user in a [child organization][3] has `org_management` permission, it does not mean that they have the same permission in the parent org. Users' roles are not shared between parent and child organizations.

**Note**: If you use a SAML identity provider, you can integrate it with Datadog for authentication, and you can map identity attributes to Datadog default and custom roles. For more information, see [SAML group mapping][4].

## Datadog default roles

Datadog Admin Role
: Users have access to billing information and the ability to revoke API keys. They can manage users and configure [read-only dashboards][5]. They can also promote standard users to administrators.

Datadog Standard Role
: Users are allowed to view and modify all monitoring features that Datadog offers, such as [dashboards][5], [monitors][6], [events][7], and [notebooks][11]. Standard users can also invite other users to organizations.

Datadog Read Only Role
: Users do not have access to edit within Datadog. This comes in handy when you'd like to share specific read-only views with a client, or when a member of one business unit needs to share a [dashboard][5] with someone outside their unit.

## Custom roles

The custom roles feature gives your organization the ability to create new roles with unique permission sets. Manage your custom roles through the Datadog site, the [Datadog Role API][8], or SAML directly. Find out below how to create, update, or delete a role. See [Datadog Role Permissions][1] for more information about available permissions. Only users with the User Access Manage permission can create or edit roles in Datadog.

### Enable custom roles

1. Navigate to [Organization Settings][9].
2. On the left side of the page, select **Roles**.
3. Click the gear in the upper right corner. The Custom Roles pop-up appears.
4. In the Custom Roles pop-up, click **Enable**.

{{< img src="account_management/rbac/enable_custom_roles.png" alt="Custom Roles pop-up with Enable button" style="width:90%;">}}

Alternatively, making a POST call to the [Create Role API endpoint][10] automatically enables custom roles for your organization.

### Create a custom role

{{< tabs >}}
{{% tab "Datadog application" %}}

To create a custom role:

1. Go to your [Datadog Roles page][1].
2. Select **New Role** in the upper right corner of the page.
3. Give a name to your role.
4. Assign a set of permissions to your role. See [Datadog Role Permissions][2] for more information about available permissions.

Once a role is created, you can [add the role to existing users][3].


[1]: https://app.datadoghq.com/access/roles
[2]: /account_management/rbac/permissions/
[3]: /account_management/users/#edit-a-user-roles
{{% /tab %}}
{{% tab "API" %}}

Find an example of how to create a role in [Create Role API Reference][1].


[1]: /api/latest/roles/#create-role
{{% /tab %}}
{{< /tabs >}}

### Update a role

{{< tabs >}}
{{% tab "Datadog application" %}}

To edit a custom role:

1. Go to your [Datadog Roles page][1].
2. Select the edit button on the role you would like to modify.
3. Modify the set of permissions for your role. See [Role Permissions][2] for more information about available permissions.
4. Save your changes.


Once a role is modified, permissions are updated for all users with the role.


[1]: https://app.datadoghq.com/access/roles
[2]: /account_management/rbac/permissions/
{{% /tab %}}
{{% tab "API" %}}

Find an example of how to update a role in [Update Role API Reference][1].


[1]: /api/latest/roles/#update-a-role
{{% /tab %}}
{{< /tabs >}}

### Clone a role

{{< tabs >}}
{{% tab "Datadog application" %}}

To clone an existing role:

1. Go to your [Datadog Roles page][1].
2. Hover over the role you would like to clone. A series of buttons appears to the right.
3. Select the clone button on the role you would like to clone.
4. Optionally modify the name or permissions of the role.
5. Click the **Save** button at the bottom.

{{< img src="account_management/rbac/clone_role.png" alt="List of two roles with Clone button highlighted" style="width:90%;">}}


[1]: https://app.datadoghq.com/access/roles
{{% /tab %}}
{{% tab "API" %}}

Find an example of how to clone a role in the [Cloning A Role API reference][1].

[1]: /api/latest/roles/#create-a-new-role-by-cloning-an-existing-role
{{% /tab %}}
{{< /tabs >}}

### Delete a role

{{< tabs >}}
{{% tab "Datadog application" %}}

To delete a custom role:

1. Go to your [Datadog Roles page][1].
2. Hover over the role you would like to delete. A series of buttons appears to the right.
3. Select the delete button on the role you would like to delete.
4. Confirm your decision.


Once a role is deleted, permissions are updated for all users with the role. Users without any roles cannot use Datadog effectively, but still maintain limited access.


[1]: https://app.datadoghq.com/access/roles
{{% /tab %}}
{{% tab "API" %}}

Find an example of how to delete a role in the [Delete Role API reference][1].


[1]: /api/latest/roles/#delete-role
{{% /tab %}}
{{< /tabs >}}

### Apply a role template

When creating or updating a role on the Datadog site, use a Datadog role template to apply a prescribed set of permissions to the role.

1. On the New Role or Edit Role page, click the **Show Role Templates** button on the right.
2. A dropdown menu populated with role templates appears.
3. From the menu, select the role template whose permissions you would like to apply to your role.
4. Click the **Apply** button.
4. Optionally make additional changes to your role.
5. Click the **Save** button.

{{< img src="account_management/rbac/role_templates.png" alt="Role Templates dropdown menu with Datadog Billing Admin Role selected" style="width:90%;">}}

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /account_management/rbac/permissions/
[2]: /account_management/rbac/granular_access/
[3]: /account_management/multi_organization/
[4]: /account_management/saml/mapping/
[5]: /dashboards/
[6]: /monitors/
[7]: /events/
[8]: /api/v2/roles/
[9]: https://app.datadoghq.com/organization-settings/
[10]: /api/latest/roles/#create-role
[11]: /notebooks
