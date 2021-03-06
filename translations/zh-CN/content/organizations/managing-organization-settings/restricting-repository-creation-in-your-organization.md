---
title: 限制在组织中创建仓库
intro: 为保护组织的数据，您可以配置在组织中创建仓库的权限。
redirect_from:
  - /articles/restricting-repository-creation-in-your-organization
  - /github/setting-up-and-managing-organizations-and-teams/restricting-repository-creation-in-your-organization
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - Organizations
  - Teams
shortTitle: 限制仓库创建
---

您可以选择成员是否可以在组织中创建仓库。 If you allow members to create repositories, you can choose which types of repositories members can create.{% ifversion fpt %} To allow members to create private repositories only, your organization must use {% data variables.product.prodname_ghe_cloud %}.{% endif %} For more information, see "[About repositories](/repositories/creating-and-managing-repositories/about-repositories#about-repository-visibility)."

组织所有者始终可以创建任何类型的仓库。

{% ifversion fpt %}企业所有者{% else %}网站管理员{% endif %} 可以限制用于组织仓库创建策略的选项。 更多信息请参阅{% ifversion fpt %}“[在企业帐户中实施仓库管理策略](/github/setting-up-and-managing-your-enterprise/enforcing-repository-management-policies-in-your-enterprise-account)”。{% else %}“[限制企业中的仓库创建](/admin/policies/enforcing-repository-management-policies-in-your-enterprise#setting-a-policy-for-repository-creation)”。{% endif %}

{% warning %}

**警告**：此设置仅限制在仓库创建时可用的可见性选项，而不会限制以后更改仓库可见性的能力。 有关限制更改现有仓库可见性的更多信息，请参阅“[限制组织的仓库可见性更改](/organizations/managing-organization-settings/restricting-repository-visibility-changes-in-your-organization)”。

{% endwarning %}

{% data reusables.organizations.internal-repos-enterprise %}

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.organizations.member-privileges %}
5. 在“Repository creation（仓库创建）”下，选择一个或多个选项。 ![仓库创建选项](/assets/images/help/organizations/repo-creation-perms-radio-buttons.png)
6. 单击 **Save（保存）**。
