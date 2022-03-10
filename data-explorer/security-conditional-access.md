---
title: Conditional Access with Azure Data Explorer
description: In this article, you learn how to enable conditional access on your Azure Data Explorer cluster.
ms.reviewer: cosh
ms.topic: how-to
ms.date: 03/10/2022
---

# Conditional Access with Azure Data Explorer

## What is Conditional Access?

Security perimeters often extend beyond an organization's network to include user and device identity. Organizations can use identity-driven signals as part of their access control decisions. You can use [Azure Active Directory(Azure AD) Conditional Access](/azure/active-directory/conditional-access/overview) to bring signals together, to make decisions, and enforce organizational policies.

Conditional Access policies at their simplest are like if-then statements. If a user wants to access a resource, then they must complete an action. For example, a data engineer wants to access Azure Data Explorer but is required to do multi-factor authentication (MFA) to access it.

In the following example, you'll learn how to configure a Conditional Access policy that enforces MFA for selected users using the Web UI. You can use the same steps to create other policies to meet your organization's security requirements.

### Prerequisites

Using this feature requires an Azure AD Premium license. To find the right license for your requirements, see [Compare available features of Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

> [!NOTE]
> Conditional Access policies are only applied to Azure Data Explorer's data plane operations and doesn't affect any control plane operations. For more information, see [Azure control and data plane](/azure/azure-resource-manager/management/control-plane-and-data-plane).

> [!TIP]
> Conditional Access policies are applied at the tenant level; hence, it's applied to all clusters in the tenant.

## Configure Conditional Access

1. Sign in to the Azure portal as a global administrator, security administrator, or Conditional Access administrator.

1. Browse to **Azure Active Directory** > **Security** > **Conditional Access**.

    :::image type="content" source="media/conditional-access/configure-select-conditional-access.png" alt-text="Select the isolated compute SKU.":::

1. Select **New policy**.
1. Give your policy a name. We recommend that organizations create a meaningful standard for the names of their policies.
1. Under **Assignments**, select **Users and groups**.
    1. Under **Include** > **Select users and groups**, select **Users and groups**, add the user or group you want to include for Conditional Access, and then select **Select**.

    :::image type="content" source="media/conditional-access/configure-assign-user.png" alt-text="Select the isolated compute SKU.":::

1. Under **Cloud apps or actions**, select **Cloud apps**.
    1. Under **Include**, select **Select apps** to see a list of all apps available for Conditional Access. Select **Azure Data Explorer** > **Select**, and then select **Done**.

    > [!TIP]
    > Please make sure you select the Azure Data Explorer app with the following GUID: .

    :::image type="content" source="media/conditional-access/configure-select-apps.png" alt-text="Select the isolated compute SKU.":::

1. Under **Conditions**, set the conditions you want to apply for all device platforms. For more information, see [Azure Active Directory Conditional Access : Conditions](/azure/active-directory/conditional-access/concept-conditional-access-conditions).

    :::image type="content" source="media/conditional-access/configure-select-conditions.png" alt-text="Select the isolated compute SKU.":::

1. Under **Access controls**, select **Grant**, select **Require multi-factor authentication**, and then select **Select**.

    :::image type="content" source="media/conditional-access/configure-grant-access.png" alt-text="Select the isolated compute SKU.":::

1. Set **Enable policy** to **On**, and then select **Save**.

    :::image type="content" source="media/conditional-access/configure-enforce-mfa.png" alt-text="Select the isolated compute SKU.":::

1. Verify the policy by asking an assigned user to access the [Web UI](https://dataexplorer.azure.com/). The user should be prompted for MFA.

     :::image type="content" source="media/conditional-access/configure-test-policy.png" alt-text="Select the isolated compute SKU.":::

## Next steps

* [Azure Data Explorer: Zero Trust Security with Conditional Access ](<!--<<link to the blog-->>>)
