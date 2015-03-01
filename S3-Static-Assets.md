## Storing Static Assets on S3

## Settings
- `STATIC_URL`
    - This is the default static url. It is now only really used in places we build
      the URL manually and can't use `reverse()`. It is set to the ugly cloudfront URL
      so that we can properly use relative protocol URLs and have HTTPS work because
      HTTPS is not currently set up to work in the pretty cloudfron URL (static.ycharts.com).
- `ABSOLUTE_STATIC_URL`
    - This is used for embeds, which we don't need relative protocol URLs, to look nice.
- `AWS_S3_CUSTOM_STATIC_DOMAIN`
    - This is used for creating the url in `S3BotoStorage`
- `AWS_STATIC_STORAGE_BUCKET_NAME`
    - This is used for the bucket name on S3 for staticfiles

## Storages
```python
class S3CachedMixin(object)
```
This mixin provides methods to get and set values in the redis manifest for cached static files. It also
provides the `app_static_files` attribute, which is the following format:

```python
{
    'path/to/static/file': (AppStaticStorage(app='app_name'), 'path/to/static/file'),
    ...
}
```
This format is the same as what **collectstatic** passes to `post_process()` and only contains unversioned
static assets. This then provides us with a storage that can open the original files and contains *only* original
files.

```python
class CachedLocalStorageWithS3Lookups(S3CachedMixin, CachedStaticFilesStorage)
```
This class provides a `post_process()` which will version the static assets and saved the hashed files in
**/sites/ycharts/static/**. It knows how to create S3 urls so that it can replace urls in CSS files with
the correct final urls.

The `post_process()` passes the `app_static_files` dict to the `CachedStaticFilesStorage.post_process()`
so that it will *only* act on the original unversioned assets in app directories and never on any other
static files that may be found.

```python
class S3CachedRemoteStorage(S3CachedMixin, S3BotoStorage)
```
This class is the `STATICFILES_STORAGE`, so in production, this class is responsible for uploading the files
to S3 and for generating the correct url when the `{% static %}` template tag is used. The **main_process_static**
command only uploads files found in `/sites/ycharts/static/` so this storage has a `post_process()` method
which uploads assets we use unversioned such as embeds, json files, or sitemaps.

## Deploying
In fabric, we add a setting `PIPELINE_MANIFEST_KEY` which is the output of

```bash
git rev-parse HEAD
```

which is the `SHA-1` hash of the latest commit.

In order to deploy, we use a custom management command, `main_process_static`. This command
subclasses Django's `collectstatic` command and does some extra stuff.

### Process Angular Directives
This is called first and processes our directives. It utilizes a method similar to how the `CachedFilesMixin`
replaces URLs in CSS files with versioned URLs. It reads the content of the correct template file and uses a
regex to replace `templateUrl: '/media/path/to/static/file.tpl'` with `template: '<div>Template content</div>'`.

### Process Pipeline Bundles
This is called before collecting the staticfiles. We run `PipelineStorage.post_process()` which bundles,
compresses, and minifies our static assets and saves the resulting files to
`/sites/ycharts/apps/main/static/compressed_js/` and `/sites/ycharts/apps/main/static/compressed_css/`.

### Process File Versions
This is called before collecting the staticfiles. We run `CachedLocalStorageWithS3Lookups.post_process()`
which versions all the app static files and saves them to `/sites/ycharts/static/`. This includes the pipeline
bundles created in the previous step. It also saves the hashed name manifest to redis.

### Collecting
This "collects" the staticfiles up to S3. This uses the `STATICFILES_STORAGE` setting which is set to
`CachedS3RemoteStorage`. This storage actually only uploads all the static files to S3.

### Remote Post-processing
This goes through all the original static files in app directories and uploads things like embeds, json files,
or sitemaps with 1 hour caching instead of 10 years.

## Templates
`django-pipeline`'s existing template tags call our storage's `url` method, which will return the
path to the original file if `DEBUG = True` or hit redis, and return the path to the versioned file.

## Embeds
Embeds are served from `http://static.ycharts.com`. These need to have conservative caching, so we change
the headers on the storage before uploading in `S3CachedRemoteStorage.post_process()`

**Example default headers**:

```json
{
    "Cache-Control": "max_age=315360000",
    "Content-Type": "application/javascript",
    "Content-Encoding": "gzip",
}
```

**Example embed/sitemaps headers**:

```json
{
    "Cache-Control": "max_age=3600",
    "Content-Type": "application/javascript",
    "Content-Encoding": "gzip",
}
```
