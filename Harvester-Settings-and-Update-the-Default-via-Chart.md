__TOC__  

==Harvester Settings Definition==

1. CRD definition.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/apis/harvesterhci.io/v1beta1/settings.go#L18C1-L35C2

```
type Setting struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	// +optional
	Value string `json:"value,omitempty"`

	// +optional
	Default string `json:"default,omitempty"`

	// +optional
	Customized bool `json:"customized,omitempty"`

	// +optional
	Source string `json:"source,omitempty"`

	Status SettingStatus `json:"status,omitempty"`
}
```

2. Local object definition.

Harvester specific settings are defined in the source code, generally with the `Default` field.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/settings/settings.go#L22

```
type Setting struct {
	Name     string
	Default  string
	ReadOnly bool
}

SupportBundleImage                     = NewSetting(SupportBundleImageName, "{}")
...
OvercommitConfig                       = NewSetting(OvercommitConfigSettingName, `{"cpu":1600,"memory":150,"storage":200}`)

```

==Harvester Settings Initializaton and Sync==

1. Local object initialzation and CRD object sync.

https://github.com/harvester/harvester/blob/3bba1d6dcc17589fe9607aff38bea7614065b8be/pkg/controller/global/settings/register.go#L87

When Harvester POD starts, it will init the local `Setting` object and sync with CRD object `settings.harvesterhci.io`:

If it is not eixsting, create a new one.

If it is existing, use the potential harvester ENV like HARVESTER_OVERCOMMIT_CONFIG to replace the `Value` field of `overcommit-config` settings. note the ENV naming. If the local object `Default` != `Default` of `settings.harvesterhci.io`, replace it.


2. How to deal with the dynamic `Default`.