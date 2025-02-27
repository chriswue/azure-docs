---
title: Create an Azure AD app and service principal in the portal
description: Create a new Azure Active Directory app and service principal to manage access to resources with role-based access control in Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 08/26/2022
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, subject-rbac-steps
---

# Use the portal to create an Azure AD application and service principal that can access resources

This article shows you how to create a new Azure Active Directory (Azure AD) application and service principal that can be used with the role-based access control. When you have applications, hosted services, or automated tools that need to access or modify resources, you can create an identity for the app. This identity is known as a service principal. Access to resources is restricted by the roles assigned to the service principal, giving you control over which resources can be accessed and at which level. For security reasons, it's always recommended to use service principals with automated tools rather than allowing them to log in with a user identity.

This article shows you how to use the portal to create the service principal in the Azure portal. It focuses on a single-tenant application where the application is intended to run within only one organization. You typically use single-tenant applications for line-of-business applications that run within your organization.  You can also [use Azure PowerShell to create a service principal](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Instead of creating a service principal, consider using managed identities for Azure resources for your application identity. If your code runs on a service that supports managed identities and accesses resources that support Azure AD authentication, managed identities are a better option for you. To learn more about managed identities for Azure resources, including which services currently support it, see [What is managed identities for Azure resources?](../managed-identities-azure-resources/overview.md).

## App registration, app objects, and service principals

There is no way to directly create a service principal using the Azure portal.  When you register an application through the Azure portal, an application object and service principal are automatically created in your home directory or tenant.  For more information on the relationship between app registration, application objects, and service principals, read [Application and service principal objects in Azure Active Directory](app-objects-and-service-principals.md).

## Permissions required for registering an app

You must have sufficient permissions to register an application with your Azure AD tenant, and assign to the application a role in your Azure subscription.

### Check Azure AD permissions

1. Select **Azure Active Directory**.
1. Find your role under **Overview**->**My feed**. If you have the **User** role, you must make sure that non-administrators can register applications.

   :::image type="content" source="media/howto-create-service-principal-portal/view-user-info.png" alt-text="Screenshot showing how to find your role.":::

1. In the left pane, select **Users** and then **User settings**.
1. Check the **App registrations** setting. This value can only be set by an administrator. If set to **Yes**, any user in the Azure AD tenant can register an app.

If the app registrations setting is set to **No**, only users with an administrator role may register these types of applications. See [Azure AD built-in roles](../roles/permissions-reference.md#all-roles) to learn about available administrator roles and the specific permissions in Azure AD that are given to each role. If your account is assigned the User role, but the app registration setting is limited to admin users, ask your administrator to either assign you one of the administrator roles that can create and manage all aspects of app registrations, or to enable users to register apps.

### Check Azure subscription permissions

In your Azure subscription, your account must have `Microsoft.Authorization/*/Write` access to assign a role to an AD app. This action is granted through the [Owner](../../role-based-access-control/built-in-roles.md#owner) role or [User Access Administrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) role. If your account is assigned the **Contributor** role, you don't have adequate permission. You will receive an error when attempting to assign the service principal a role.

To check your subscription permissions:

1. Search for and select **Subscriptions**, or select **Subscriptions** on the **Home** page.

   :::image type="content" source="media/howto-create-service-principal-portal/select-subscription.png" alt-text="Screenshot how to search subscription permissions.":::

1. Select the subscription you want to create the service principal in.

   :::image type="content" source="media/howto-create-service-principal-portal/select-one-subscription.png" alt-text="Select subscription for assignment.":::

   If you don't see the subscription you're looking for, select **global subscriptions filter**. Make sure the subscription you want is selected for the portal.

1. Select **My permissions**. Then, select **Click here to view complete access details for this subscription**.

   :::image type="content" source="media/howto-create-service-principal-portal/view-details.png" alt-text="Select the subscription you want to create the service principal in.":::

1. Select **Role assignments** to view your assigned roles, and determine if you have adequate permissions to assign a role to an AD app. If not, ask your subscription administrator to add you to User Access Administrator role. In the following image, the user is assigned the Owner role, which means that user has adequate permissions.

   :::image type="content" source="media/howto-create-service-principal-portal/view-user-role.png" alt-text="Screenshot showing the user is assigned the Owner role.":::

## Register an application with Azure AD and create a service principal

Let's jump straight into creating the identity. If you run into a problem, check the [required permissions](#permissions-required-for-registering-an-app) to make sure your account can create the identity.

1. Sign in to your Azure Account through the <a href="https://portal.azure.com/" target="_blank">Azure portal</a>.
1. Select **Azure Active Directory**.
1. Select **App registrations**.
1. Select **New registration**.
1. Name the application, for example "example-app". Select a supported account type, which determines who can use the application. Under **Redirect URI**, select **Web** for the type of application you want to create. Enter the URI where the access token is sent to. You can't create credentials for a [Native application](../app-proxy/application-proxy-configure-native-client-application.md). You can't use that type for an automated application. After setting the values, select **Register**.

   :::image type="content" source="media/howto-create-service-principal-portal/create-app.png" alt-text="Type a name for your application.":::

You've created your Azure AD application and service principal.

> [!NOTE]
> You can register multiple applications with the same name in Azure AD, but the applications must have different Application (client) IDs.

## Assign a role to the application

To access resources in your subscription, you must assign a role to the application. Decide which role offers the right permissions for the application. To learn about the available roles, see [Azure built-in roles](../../role-based-access-control/built-in-roles.md).

You can set the scope at the level of the subscription, resource group, or resource. Permissions are inherited to lower levels of scope. For example, adding an application to the *Reader* role for a resource group means it can read the resource group and any resources it contains.

1. In the Azure portal, select the level of scope you wish to assign the application to. For example, to assign a role at the subscription scope, search for and select **Subscriptions**, or select **Subscriptions** on the **Home** page.

   :::image type="content" source="media/howto-create-service-principal-portal/select-subscription.png" alt-text="For example, assign a role at the subscription scope.":::

1. Select the particular subscription to assign the application to.

   :::image type="content" source="media/howto-create-service-principal-portal/select-one-subscription.png" alt-text="Select subscription for assignment.":::

   If you don't see the subscription you're looking for, select **global subscriptions filter**. Make sure the subscription you want is selected for the portal.

1. Select **Access control (IAM)**.
1. Select **Add** > **Add role assignment** to open the **Add role assignment** page.
1. In the **Role** tab, select the role you wish to assign to the application in the list. For example, to allow the application to execute actions like **reboot**, **start** and **stop** instances, select the **Contributor** role.  Read more about the [available roles](../../role-based-access-control/built-in-roles.md).

    Select the **Next** button to move to the **Members** tab. Select **Assign access to**-> **User, group, or service principal** and then select **Select members**. By default, Azure AD applications aren't displayed in the available options. To find your application, search by name (for example, "example-app") and select it from the returned list. Click the **Select** button. Then click the **Review + assign** button.

    :::image type="content" source="media/howto-create-service-principal-portal/add-role-assignment.png" alt-text="Screenshot showing role assignment.":::
 
Your service principal is set up. You can start using it to run your scripts or apps. To manage your service principal (permissions, user consented permissions, see which users have consented, review permissions, see sign in information, and more), go to **Enterprise applications**.

The next section shows how to get values that are needed when signing in programmatically.

## Get tenant and app ID values for signing in

When programmatically signing in, pass the tenant ID with your authentication request and the application ID.  You also need a certificate or an authentication key (described in the following section). To get those values, use the following steps:

1. Select **Azure Active Directory**.
1. From **App registrations** in Azure AD, select your application.
1. Copy the Directory (tenant) ID and store it in your application code.

    :::image type="content" source="media/howto-create-service-principal-portal/copy-tenant-id.png" alt-text="Copy the directory (tenant ID) and store it in your app code.":::

    The directory (tenant) ID can also be found in the default directory overview page.

1. Copy the **Application ID** and store it in your application code.

   :::image type="content" source="media/howto-create-service-principal-portal/copy-app-id.png" alt-text="Copy the application (client) ID.":::

## Authentication: Two options

There are two types of authentication available for service principals: password-based authentication (application secret) and certificate-based authentication. *We recommend using a certificate*, but you can also create an application secret.

### Option 1: Upload a certificate

You can use an existing certificate if you have one.  Optionally, you can create a self-signed certificate for *testing purposes only*. To create a self-signed certificate, open PowerShell and run [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) with the following parameters to create the cert in the user certificate store on your computer:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Export this certificate to a file using the [Manage User Certificate](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC snap-in accessible from the Windows Control Panel.

1. Select **Run** from the **Start** menu, and then enter **certmgr.msc**.

   The Certificate Manager tool for the current user appears.

1. To view your certificates, under **Certificates - Current User** in the left pane, expand the **Personal** directory.
1. Right-click on the cert you created, select **All tasks->Export**.
1. Follow the Certificate Export wizard.  Do not export the private key, and export to a .CER file.

To upload the certificate:

1. Select **Azure Active Directory**.
1. From **App registrations** in Azure AD, select your application.
1. Select **Certificates & secrets**.
1. Select **Certificates** > **Upload certificate** and select the certificate (an existing certificate or the self-signed certificate you exported).

    :::image type="content" source="media/howto-create-service-principal-portal/upload-cert.png" alt-text="Select Upload certificate and select the one you want to add.":::

1. Select **Add**.

After registering the certificate with your application in the application registration portal, enable the client application code to use the certificate.

### Option 2: Create a new application secret

If you choose not to use a certificate, you can create a new application secret.

1. Select **Azure Active Directory**.
1. From **App registrations** in Azure AD, select your application.
1. Select **Certificates & secrets**.
1. Select **Client secrets -> New client secret**.
1. Provide a description of the secret, and a duration. When done, select **Add**.

   After saving the client secret, the value of the client secret is displayed. Copy this value because you won't be able to retrieve the key later. You will provide the key value with the application ID to sign in as the application. Store the key value where your application can retrieve it.

   :::image type="content" source="media/howto-create-service-principal-portal/copy-secret.png" alt-text="Copy the secret value because you can't retrieve this later.":::

## Configure access policies on resources
Keep in mind, you might need to configure additional permissions on resources that your application needs to access. For example, you must also [update a key vault's access policies](../../key-vault/general/security-features.md#privileged-access) to give your application access to keys, secrets, or certificates.

1. In the <a href="https://portal.azure.com/" target="_blank">Azure portal</a>, navigate to your key vault and select **Access policies**.
1. Select **Add access policy**, then select the key, secret, and certificate permissions you want to grant your application.  Select the service principal you created previously.
1. Select **Add** to add the access policy, then **Save** to commit your changes.
    ![Add access policy](./media/howto-create-service-principal-portal/add-access-policy.png)

## Next steps
* Learn how to [use Azure PowerShell to create a service principal](howto-authenticate-service-principal-powershell.md).
* To learn about specifying security policies, see [Azure role-based access control (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* For a list of available actions that can be granted or denied to users, see [Azure Resource Manager Resource Provider operations](../../role-based-access-control/resource-provider-operations.md).
* For information about working with app registrations by using **Microsoft Graph**, see the [Applications](/graph/api/resources/application) API reference.
