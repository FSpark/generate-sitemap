# Generate Sitemap

[![build](https://github.com/cicirello/generate-sitemap/workflows/build/badge.svg)](https://github.com/cicirello/generate-sitemap/actions?query=workflow%3Abuild)

This action generates a sitemap for a website hosted on GitHub
Pages. It supports both xml and txt sitemaps. When generating
an xml sitemap, it uses the last commit date of each file to
generate the lastmod tag in the sitemap entry. It can include
html as well as pdf files in the sitemap, and has inputs to
control the included file types (defaults include both html
and pdf files in the sitemap). It skips over html files that
contain `<meta name="robots" content="noindex">`. It otherwise
does not currently attempt to respect a robots.txt file.

It is designed to be used in combination with other GitHub
Actions. For example, it does not commit and push the generated
sitemap. See the [examples](#examples) for examples of combining
with other actions.

## Requirements

This action relies on `actions/checkout@v2` with `fetch-depth: 0`.
Setting the `fetch-depth` to 0 for the checkout action ensures
that the `generate-sitemap` action will have access to the commit
history, which is used for generating the `<lastmod>` tags in the
`sitemap.xml` file.  If you instead use the default when applying the
checkout action, the `<lastmod>` tags will be incorrect.  So be
sure to include the following as a step in your workflow:

```yml
    steps:
    - name: Checkout the repo
      uses: actions/checkout@v2
      with:
        fetch-depth: 0 
```

## Inputs

### `path-to-root`

**Required** The path to the root of the website relative to the
root of the repository. Default `.` is appropriate in most cases,
such as whenever the root of your Pages site is the root of the
repository itself. If you are using this for a GitHub Pages site
in the `docs` directory, such as for a documentation website, then
just pass `docs` for this input.

### `base-url-path`

**Required** This is the url to your website. You must specify this
for your sitemap to be meaningful.  It defaults
to `https://web.address.of.your.nifty.website/` for demonstration
purposes.

### `include-html`

**Required** This flag determines whether html files are included in
your sitemap. Default: `true`.

### `include-pdf`

**Required** This flag determines whether pdf files are included in
your sitemap. Default: `true`.

### `sitemap-format`

**Required** Use this to specify the sitemap format. Default: `xml`.
The `sitemap.xml` generated by the default will contain lastmod dates
that are generated using the last commit dates of each file. Setting 
this input to anything other than `xml` will generate a plain text 
`sitemap.txt` simply listing the urls.

## Outputs

### `sitemap-path`

The generated sitemap is placed in the root of the website. This 
output is the path to the generated sitemap file relative to the
root of the repository. If you didn't use the `path-to-root` input, then
this output should simply be the name of the sitemap file (`sitemap.xml`
or `sitemap.txt`).

### `url-count`

This output provides the number of urls in the sitemap.

### `excluded-count`

This output provides the number of urls excluded from the sitemap due
to `<meta name="robots" content="noindex">` within html files.

## Examples