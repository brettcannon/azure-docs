---
title: Language customization in Azure AD B2C| Microsoft Docs
description: Learn about customizing the language experience. 
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu

---

# Language customization in Azure Active Directory B2C

>[!NOTE]
>This feature is in public preview.
>

Language customization in Azure Active Directory B2C (Azure AD B2C) allows your policy to accommodate different languages to suit your customer needs.  Microsoft provides the translations for [36 languages](#supported-languages), but you can also provide your own translations for any language. Even if your experience is provided for only a single language, you can customize any text on the pages.  

## How language customization works
You use language customization to select which languages your user journey is available in. After the feature is enabled, you can provide the query string parameter, `ui_locales`, from your application. When you call into Azure AD B2C, your page is translated to the locale that you have indicated. This type of configuration gives you complete control over the languages in your user journey and ignores the language settings of the customer's browser. 

You might not need that level of control over what languages your customer sees. If you don't provide a `ui_locales` parameter, the customer's experience is dictated by their browser's settings.  You can still control which languages your user journey is translated to by adding it as a supported language. If a customer's browser is set to show a language that you don't want to support, then the language that you selected as a default in supported cultures is shown instead.

- **ui-locales specified language**: After you enable language customization, your user journey is translated to the language that's specified here.
- **Browser-requested language**: If no `ui_locales` parameter was specified, your user journey is translated to the browser-requested language, *if the language is supported*.
- **Policy default language**: If the browser doesn't specify a language, or it specifies one that is not supported, the user journey is translated to the policy default language.

>[!NOTE]
>If you're using custom user attributes, you need to provide your own translations. For more information, see [Customize your strings](#customize-your-strings).
>

## Support requested languages for ui_locales 
Policies that were created before the general availability of language customization need to enable this feature first. Policies that were created after have language customization enabled by default. 

When you enable language customization on a policy, you can control the language of the user journey by adding the `ui_locales` parameter.
1. [Go to the B2C features page on the Azure portal](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Browse to a policy that you want to enable for translations.
3. Select **Language customization**.  
4. Select **Enable language customization**.
5. Read the information in the dialog box, and select **Yes**.

## Select which languages in your user journey are enabled 
Enable a set of languages for your user journey to be translated to when the `ui_locales` parameter is not provided.
1. Ensure that your policy has language customization enabled from previous instructions.
2. From the **Edit policy** page, select **Language customization**.
3. Select a language that you want to support.
4. In the properties pane, change **Enabled** to **Yes**.  
5. Select **Save** at the top of the properties pane.

>[!NOTE]
>If a `ui_locales` parameter is not provided, the page is translated to the customer's browser language only if it is enabled.
>

## Customize your strings
Language customization enables you to customize any string in your user journey.
1. Ensure that your policy has language customization enabled from the previous instructions.
2. From the **Edit policy** page, select **Language customization**.
3. Select the language that you want to customize.
4. Select the page that you want to edit.
5. Select **Download defaults** (or **Download overrides** if you have previously edited this language). 

These steps give you a JSON file that you can use to start editing your strings.

### Change any string on the page
1. Open the JSON file downloaded from previous instructions in a JSON editor.
2. Find the element that you want to change.  You can find `StringId` for the string you're looking for, or look for the `Value` attribute that you want to change.
3. Update the `Value` attribute with what you want displayed.
4. For every string that you want to change, change `Override` to `true`.
5. Save the file and upload your changes. (You can find the upload control in the same place as where you downloaded the JSON file.) 

>[!IMPORTANT]
>If you need to override a string, make sure to set the `Override` value to `true`.  If the value isn't changed, the entry is ignored. 
>

### Change extension attributes
If you want to change the string for a custom user attribute, or you want to add one to the JSON, it's in the following format:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Replace `<ExtensionAttribute>` with the name of your custom user attribute.  

Replace `<ExtensionAttributeValue>` with the new string to be displayed.

### Provide a list of values by using LocalizedCollections
If you want to provide a set list of values for responses, you need to create a `LocalizedCollections` attribute.  `LocalizedCollections` is an array of `Name` and `Value` pairs. To add `LocalizedCollections`, use the following format:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` is the user attribute that this `LocalizedCollections` attribute is a response to.
* `Name` is the value that's shown to the user.
* `Value` is what is returned in the claim when this option is selected.

### Upload your changes
1. After you complete the changes to your JSON file, go back to your B2C tenant.
2. From the **Edit policy** page, select **Language customization**.
3. Select the language that you want to translate to.
4. Select the page where you want to provide translations.
5. Select the folder icon, and select the JSON file to upload.
 
The changes are saved to your policy automatically.

## Customize the page UI by using language customization

There are two ways to localize your HTML content. One way is to turn on [language customization](active-directory-b2c-reference-language-customization.md). Enabling this feature allows Azure AD B2C to forward the Open ID Connect parameter, `ui-locales`, to your endpoint.  Your content server can use this parameter to provide customized HTML pages that are language specific.

Alternatively, you can pull content from different places based on the locale that's used. In your CORS-enabled endpoint, you can set up a folder structure to host content for specific languages. You'll call the right one if you use the wildcard value `{Culture:RFC5646}`.  For example, assume that this is your custom page URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
You can load the page in `fr`. When the page pulls HTML and CSS content, it's pulling from:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## Add custom locales

You can also add languages that Microsoft currently does not provide translations for. You'll need to provide the translations for all the strings in the policy.

1. From the **Edit policy** page, select **Language customization**.
2. Select **Add custom language** from the top of the page.
3. In the context pane that opens, identify which language you're providing translations for by entering a valid locale code.
4. For each page, you can download a set of overrides for English and work on the translations.
5. After you're done with the JSON files, you can upload them for each page.
6. Select **Enable**, and your policy can now show this language for your users.
7. Save the language.

## Additional information

### Page UI customization labels as overrides
When you enable language customization, your previous edits for labels using page UI customization are persisted in a JSON file for English (en). You can continue to change your labels and other strings by uploading language resources in language customization.
### Up-to-date translations
Microsoft is committed to providing the most up-to-date translations for your use. Microsoft continuously improves translations and keeps them in compliance for you. Microsoft will identify bugs and changes in global terminology and make updates that will work seamlessly in your user journey.
### Support for right-to-left languages
Microsoft currently doesn't provide support for right-to-left languages. If you need this feature, please vote for it on [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### Social identity provider translations
Microsoft provides the `ui_locales` OIDC parameter to social logins. But some social identity providers, including Facebook and Google, don't honor them. 
### Browser behavior
Chrome and Firefox both request for their set language. If it's a supported language, it's displayed before the default. Edge currently does not request a language and goes straight to the default language.

### Supported languages

| Language              | Language code |
|-----------------------|---------------|
| Bangla                | bn            |
| Czech                 | cs            |
| Danish                | da            |
| German                | de            |
| Greek                 | el            |
| English               | en            |
| Spanish               | es            |
| Finnish               | fi            |
| French                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croatian              | hr            |
| Hungarian             | hu            |
| Italian               | it            |
| Japanese              | ja            |
| Kannada               | kn            |
| Korean                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malay                 | ms            |
| Norwegian Bokmal      | nb            |
| Dutch                 | nl            |
| Punjabi               | pa            |
| Polish                | pl            |
| Portuguese - Brazil   | pt-br         |
| Portuguese - Portugal | pt-pt         |
| Romanian              | ro            |
| Russian               | ru            |
| Slovak                | sk            |
| Swedish               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Thai                  | th            |
| Turkish               | tr            |
| Chinese - Simplified  | zh-hans       |
| Chinese - Traditional | zh-hant       |
