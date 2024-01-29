# Rhize documentation author's guide

How to contribute to the Rhize docs.

## Create new page

To create a new page, run this command:

```shell
hugo new content/<PATH>/<TO>/<PAGE>.md
```

## Style

The docs follow the [Google Developer's Style Guide](https://developers.google.com/), a widely used standard for developer docs. 

The Vale CI checker also has a spellchecker for our specific special terms. Check `github/workflows/Vocabularies`.

## Shortcodes

_Shortcodes_ are built in functions that you call in the markdown file.
Hugo processes their instructions to render something on the page.

Common use cases are for style, as with notices, and for templating.

### Abbreviation

The abbreviation provides tooltips, using definitions given in `data/termbase.yaml`.

The `abbr`  syntax is as follows:

```html
{{< abbr "<TERM>" >}} 
```

For example:

```
These pages describe the elements to make a Rhize {{< abbr "bpmn" >}} workflow,
```

Renders as:

![image](https://github.com/libremfg/libremfg.github.io/assets/47385188/2394da77-821b-4379-8814-df2476f6e25c)


### Expandable

Hide extensive details in an accordion:

```html
{{< expandable title="Click for more details" >}}
This text should be hidden initially.

Put markdown here

{{< /expandable >}}

```

### Notices

Use notices with ```{{% notice $TYPE %}}```:

For example:

```html
{{% notice note %}}

Just keep this in mind.

{{% /notice %}}
```

Rhize docs have three types of notices:

- **Note**. For side information
- **Caution**. To tell people to be careful or understand limitations
- **Warning**. When an action is irreversible.

For example:


![](https://user-images.githubusercontent.com/47385188/282773723-3ac4671a-3cc1-42fe-b27c-340402704fd5.png)

is rendered from:

```html

{{% notice note %}}

Just keep this in mind.

{{% /notice %}}

{{% notice caution %}}

Be careful, this library could change.

{{% /notice %}}

{{% notice warning %}}

This action is irreversible.

{{% /notice %}}
```



## Use templates

Each document should have a specific purpose:
At the time of writing, the templated types are `concepts`, `how-to`, `reference`, and `releases`.
To decide which topic you want to write, consult [this Diagram](https://wellshapedwords.com/images/diataxis.svg "diataxis-topic picker")

Each general purpose has template defined in the `archetypes` directory.
For your convenience, use the `archetypes` to avoid writing bolerplate properties:

1. Navigate to the root of this repository.
2. Run `hugo new content/<type>/<name-of-document>.md`


Topics are clustered in groups of the sidebar, corresponding to directories of the same name.
If you want to make a type in a different content directory, use the `--kind` flag:

```sh
hugo new content --kind reference get-started/fake-reference-page.md
```

To learn how to update templates, read the [Hugo Archetypes documentation](https://gohugo.io/content-management/archetypes/). 
