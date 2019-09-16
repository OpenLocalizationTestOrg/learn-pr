In this module, you have explored the OpenID Connect concepts for authentication.

By using an example application, you used the OpenID Connect ASP.NET OWIN middleware to sign in users from a single Azure Active Directory (Azure AD) tenant. Users now sign in with the same accounts that they use for Office 365.

## Clean up

The sandbox will clean up your resources automatically when you finish this module. But the Azure AD tenant that you created must be deleted manually.

To delete the Azure AD tenant, follow these steps:

1. In the [Azure portal](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true), in the top bar, select your account, and then select **Switch directory**.
1. Select the **Learn Module AAD Tenant** directory that you created in unit 4.
1. In the left pane, select **Azure Active Directory**, select **App Registrations**, and then select the **WebApp-OpenIDConnect-DotNet** registration.
1. Select **Delete**, and then select **Yes**.
1. In the left pane, select **Azure Active Directory**. Then under **Manage**, select **Properties**.
1. Under **Access management for Azure resources**, select **Yes**, and then select **Save**.
1. In the upper right of the portal, select your user account, and then select **Sign out**.
1. Sign in with your normal credentials. In the top bar, select your account, and then select **Switch directory**.
1. Select the **Learn Module AAD Tenant** directory that you created in unit 4.
1. In the left pane, select **Azure Active Directory**, select **Delete directory**, and then select **Delete**.
1. In the upper right of the portal, select your user account, and then select **Sign out**.

## Further reading

- [Authorize access to web applications by using OpenID Connect and Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-openid-connect-code)
- [OpenID Connect specification](https://openid.net/specs/openid-connect-core-1_0.html)
- [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
- [Azure Active Directory access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
