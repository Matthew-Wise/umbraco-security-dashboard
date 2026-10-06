# Reading Exposure Markers from Github Advisories

Not all advisories create a security exposure for all sites in all configurations. 
It's often the case that the site's configuration will prevent the exposure from being exploited. 

In responding to advisories, site owners should focus first on the sites where a real exposure exists. 
To help with this, the Security Dashboard uses an extensible Exposure Check system to examine the site's 
configuration and automatically mark an exposure as mitigated if the site isn't exposed due to its configuration.

## Exposure Markers in Github Advisories

To enable this, the Github advisory should include an 'Exposure' section that includes keywords to trigger exposure
checks. The ExposureKeywordParser reads the Advisory description and looks for an `Exposure` header marked with 
this Markdown structure: 

`### Exposure\n`

Within this section, it expects a bulleted list with the exposure keyword in bold. 

Currently, there are two exposure checks that have been added based on past advisories: 

* **Content Delivery API**: If the exposure keyword is **Content Delivery API**, then the exposure will be considered
as auto-mitigated if the Content Delivery API is disabled.
* **Non-Admin Backoffice Users**: If the exposure keyword is **Non-Admin Backoffice Users**, then the exposure will be 
considered as auto-mitigated if there are no non-administrator Backoffice users. These are typically privilege
escalation exploits, but irrelevant if there is no user to escalate privilege from.

The Exposure section should also include human-readable descriptions, which are helpful for users but ignored by the 
ExposureCheckEvaluator. 

## Example Advisory Description
(based on [GHSA-7pj9-jmp9-p86q](https://github.com/umbraco/Umbraco-CMS/security/advisories/GHSA-7pj9-jmp9-p86q) Authorization bypass allows low-privileged users to delete media outside their start node)

----

### Impact
An access control weakness affects media management in the Backoffice. Under certain conditions, a low-privileged user with restricted media permissions could cause media files outside the part of the media library they are authorised to access to be deleted. Exploitation requires an authenticated Backoffice account with access to the Media section.

Deleted files may not be recoverable without a backup.

### Patches
The vulnerability is fixed in Umbraco CMS 17.7.1 and Umbraco CMS 18.2.1. Earlier releases in the 17.x and 18.x lines are affected, and users should upgrade to the patched version for their line.

The fix adds additional validation before media files are modified or removed, so that operations only affect files belonging to the media item being processed. The validation also covers existing media.

Custom media path schemes. If your site, or a package you use, provides a custom media path scheme, upgrading alone does not fully protect you. See https://docs.umbraco.com/umbraco-cms/17.latest/extend-your-project/server-side-extensions/filesystemproviders#mediapath-scheme for more details. Custom media path schemes are not a typical extension point used for Umbraco websites.

### Workarounds
There is no configuration change that fully mitigates this issue, so upgrading is the recommended remediation. Until you can upgrade, reduce exposure by limiting access to the Media section, including API users, to trusted users. Also review that existing Backoffice and API accounts only have the permissions they require.

### Exposure

* **Non-Admin Backoffice Users**: Exploiting this weakness requires a non-admin Backoffice user. It cannot be exploited 
if there are no low-priveleged users.