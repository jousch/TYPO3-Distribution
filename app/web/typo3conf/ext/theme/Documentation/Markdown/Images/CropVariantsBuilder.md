Back to [Index](../Index.md) / Back to [Images Index](Index.md)

---

# CropVariants Builder

> `JosefGlatz\Theme\Backend\CropVariants\Builder->getInstance()` FTW :-]


1. [Example 1](#example-1-set-a-global-or-default-cropvariants-configuration)
1. [aspectRatio presets](#predefined-aspectratios)
1. [coverArea presets](#predefined-coverareas)
1. [cropArea presets](#predefined-cropareas)
1. [List of default cropVariants]()

You get a nice overview just by comparing before/after configuration scenarios.

---

## Example 1: Set a global-or-default cropVariants configuration

The "default" cropVariants configuration is set as a project default. 6 allowed aspect ratios are configured.

### Before
`EXT:theme/Configuration/TCA/Overrides/sys_file_reference.php`

***The downside:*
* All options are set without defaults
* writing the configuration is error-prone (because you have no autocompletion)
* the cropArea is set manually (no centralized preset)
* allowed aspect ratios are set manually (no centralized presets)
* manual title option string

```php
<?php
defined('TYPO3_MODE') || die('Access denied.');

call_user_func(
    function ($extKey, $table) {
        $languageFileBePrefix = 'LLL:EXT:' . $extKey . '/Resources/Private/Language/locallang_BackendGeneral.xlf:';

        $tca = [
            'columns' => [
                'crop' => [
                    'config' => [
                        'cropVariants' => [
                            'default' => [
                                'title' => $languageFileBePrefix . 'crop_variants.default.label',
                                'coverAreas' => [],
                                'cropArea' => [
                                    'x' => '0.0',
                                    'y' => '0.0',
                                    'width' => '1.0',
                                    'height' => '1.0'
                                ],
                                'allowedAspectRatios' => [
                                    '3:2' => [
                                        'title' => 'LLL:EXT:lang/Resources/Private/Language/locallang_wizards.xlf:imwizard.ratio.3_2',
                                        'value' => 3 / 2
                                    ],
                                    '2:3' => [
                                        'title' => '2:3',
                                        'value' => 2 / 3
                                    ],
                                    '4:3' => [
                                        'title' => 'LLL:EXT:lang/Resources/Private/Language/locallang_wizards.xlf:imwizard.ratio.4_3',
                                        'value' => 4 / 3
                                    ],
                                    '3:4' => [
                                        'title' => '3:4',
                                        'value' => 3 / 4
                                    ],
                                    '1:1' => [
                                        'title' => 'LLL:EXT:lang/Resources/Private/Language/locallang_wizards.xlf:imwizard.ratio.1_1',
                                        'value' => 1.0
                                    ],
                                    'NaN' => [
                                        'title' => 'LLL:EXT:lang/Resources/Private/Language/locallang_wizards.xlf:imwizard.ratio.free',
                                        'value' => 0.0
                                    ],
                                ],
                                'selectedRatio' => 'NaN'
                            ],
                        ],
                    ],
                ],
            ]
        ];
        $GLOBALS['TCA'][$table] = array_replace_recursive($GLOBALS['TCA'][$table], $tca);
    },
    'theme',
    'sys_file_reference'
);
```

### Afterwards
`EXT:theme/Configuration/TCA/Overrides/sys_file_reference.php`

**The advantages:**
* Enjoying IDE auto completion
* easy to read
* adding a cropVariant..
** the cropVariant constructor tries to set title LLL strings based on the given CropVariant name
** the desired cropArea preset is automatically set to default (of course, you can set the cropArea based on presets)
** all default allowedAspectRatios are set with one line of code
** setting the selectedRatio is super easy
** Retrieve final cropVariant configuration with `get()` method
* finally persist the configuration just via one line of code


```php
<?php
defined('TYPO3_MODE') || die('Access denied.');

call_user_func(
    function ($extKey, $table) {
        \JosefGlatz\Theme\Backend\CropVariants\Builder::getInstance($table, 'crop')
            ->addCropVariant(
                \JosefGlatz\Theme\Backend\CropVariants\CropVariant::create('default')
                    ->setCropArea(\JosefGlatz\Theme\Backend\CropVariants\Defaults\CropArea::get())
                    ->addAllowedAspectRatios(\JosefGlatz\Theme\Backend\CropVariants\Defaults\AspectRatio::getDefaults())
                    ->setSelectedRatio('NaN')
                    ->get()
            )
            ->persistToDefaultTableTca();
    },
    'theme',
    'sys_file_reference'
);
```
---

## Predefined aspectRatios

| Key      | Description                                                              |
| -------- | ------------------------------------------------------------------------ |
| `3:1`    | Often used for wide sujet images                                         |
| `2:1`    | Often used for wide sujet images                                         |
| `1.91:1` | Suggested by Facebook (and Twitter) for open graph / twitter card images |
| `16:9`   | Common video standard                                                    |
| `3:2`    | Common D-/SLR format photography                                         |
| `2:3`    | *(portrait)*                                                             |
| `4:3`    | Common point and shoot format photography                                |
| `3:4`    | *(portrait)*                                                             |
| `5:4`    | Common large and medium format photography                               |
| `4:5`    | *(portrait)*                                                             |
| `1:1`    | Square image format                                                      |
| `NaN`    | Free ratio (no ratio limitation)                                         |

## Predefined coverAreas

| Key           | Usage Scenario                                                                                |
| ------------- | --------------------------------------------------------------------------------------------- |
| `1_1_circle`  | Can be used for square images with CSS `border-radius: 50%` for example.                      |
| `lower_third` | Can be used for images where the lower third of the image area is overlayed by a DOM element. |

## Predefined cropAreas

| Key       | Description                                     |
| --------- | ----------------------------------------------- |
| `default` | Default (biggest possible) (X0, Y0, W1.0, H1.0) |

## List of default cropVariants

The list of default cropVariants contains an array with all as default set cropVariants set for `sys_file_reference.crop` for this TYPO3 instance/project.

| Key       |
| --------- |
| `default` |

> The list of default cropVariants is actually used if you use `disableDefaultCropVariants()` method.