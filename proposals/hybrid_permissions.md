# Proposal: Hybrid Permissions for Web Extensions

** Proposal Summary**

Introduce a concept of “hybrid_permissions” which will offer existing extensions a simplified new-install experience by prompting users with both (1) required and (2) optional permissions and maintain current behavior for “optional_permissions” for users who already have the extension installed.

Extension manifest file example:

```json
{
  "name": "Permissions Extension",
  "hybrid_permissions": [
    "notifications"
  ]
}
```

**Document Metadata**

**Author:** @oleksiilevzhynskyi

**Sponsoring Browser:** N/A

**Contributors:** N/A

**Created:** 2025-02-13

**Related Issues:** N/A

## Motivation

### Objective

Today, developers are forced to choose between the following 2 options:

1. Add a new permission as  ‘required’ which will (1) disable the extension for 100% of current users until they take manual steps to grant the new permission. This is not a viable option for extension with large user install bases, as [The UI for reapproval of such permission is very unclear](https://developer.chrome.com/docs/extensions/develop/concepts/permission-warnings), and in most cases, it means a loss of most active users.

On the manifest-files level, it’s a “permissions” property that represents list of permissions user agreed upon installation.

2. Add new permissions as ‘optional’ which will limit access to new features until the user grants access to the new permission.  <—  This is not optimal as it creates a confusing experience for new users who reviewed and accepted permissions a few seconds earlier while installing the extension.

On the manifest-files level, it’s a “optional_permissions” property that represents a list of permissions that the extension may request at any point, and the user whether agrees or declines them.

We are proposing a third option: Hybrid-Permission to improve the user experience for new users.

For New Installations: We would like to offer a single permission prompt containing all required and hybrid permission requests.
For Current users: No change from current optional behavior.

User Benefits
By supporting hybrid permissions, we can offer the best user experience by being open and upfront about the permissions our application will require to function optimally as well as reducing user confusion with a single prompt as opposed to multiple.

User experience reports consistently show that many users experience confusion or hesitation when encountering permission requests, particularly when these requests are not anticipated (shortly after installing our application) or seem redundant.

<TODO: Here is a link to a video showing the behavior that we would like to avoid with the support for hybrid permissions.>

#### Use Cases

Use-case: Simplify/Streamline onboarding of browser extensions for new users by offering upfront notice of permissions required to use Grammarly features.

### Known Consumers

Support for Hyrbid Permissions will help Grammarly improve the user experience, but this support will also improve the broader extension ecosystem as many well-established extensions struggle with the same problem.

## Specification

### Schema

[Existing “chrome.permissions” behavior](https://developer.chrome.com/docs/extensions/reference/api/permissions#step_3_request_optional_permissions) is completely compatible with “hybrid” permissions. 

The only difference is that hybrid permissions should behave like permanent permissions and cannot be removed. This means that “Chrome.permissions.remove” should return “false” and print an exception.

```
<remove permission example>
```

### Behavior

Hybrid permissions should be requested alongside regular “permissions” with the same UI for new installs.

<UI example>

For existing installs (extension update flow), nothing should be requested. However, the extension should check and request new permission on runtime.

Overall, the extension should tread the hybrid permission as optional. The only that’s going to be changed is the permissions request flow. Instead of requesting on demand, “Hybrid” provides the ability to request permanent “optional” permissions in the installation flow.

### New Permissions

The proposal doesn’t introduce any new permission. However, it’s introducing a new behavior for requesting existing permission. 

All existing optional permissions are qualified to be “hybrid.” (Exceptions are the list of permissions that cannot be optional: debugger, declarativeNetRequest, dev tools, geolocation, DNS, proxy, tts, ttsEngine, wallpaper.)

### Manifest File Changes

Introduce “hybrid_permissions” that will be treated as regular permissions for new installs but behave like “optional_permissions” for existing users.

Extension manifest file example:

```json
{
  "name": "Permissions Extension",
  "hybrid_permissions": [
    "notifications"
  ]
}
```

## Security and Privacy

### Exposed Sensitive Data

This proposal doesn't change the existing browser API or expose PII data.

### Abuse Mitigations

The proposal doesn't bring any additional possibilities for abuse. For already installed extensions, "hybrid" permissions behave like optional and should be explicitly requested. Permissions are shown as "permanent" for new installations and are explicitly reviewed by the user upon installation.

### Additional Security Considerations

N/A

## Alternatives

### Existing Workarounds

Additional (new) permissions can be added as required, but this will result in 100% of users having the extension become disabled which is a catastrophic result. Hence we are not aware of any viable workarounds for extensions that are deployed to users in production.

This proposal is purely focused on improving user experience working with Extensions. By nature, extensions extend browser functionality and lack possibilities to promote their functionality unless it was graded during the installation phase. The idea is to limit user friction during extension setup and simplify permission granting.
 
There is a way to use option permissions to introduce a new permission. However, it limits visibility as it usually requires direct interaction with Extension, which may be limited due to the nature of available UX.

### Open Web API

N/A

## Implementation Notes

N/A

## Future Work

N/A

