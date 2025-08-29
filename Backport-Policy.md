We use the severity of issues to determine their eligibility for backporting. The rules are as follows:

> [!Note]
> The severity labels for the Harvester project, as defined on the GitHub repository (https://github.com/harvester/harvester/labels?q=severity%2F), indicate the impact level of issues.

* Feature (`area/feature`) and Enhancement (`area/enhancement`) issues are not eligible for backporting to maintained branches. 
* Bug (`area/bug`) issues with high severity (`severity/1`, `severity/2`) or `regression` are eligible for backporting unless they cause breaking changes or compatibility concerns. 
* For UI bug issues with `severity/3` are also eligible for backporting, as the UI is the primary user interface. 