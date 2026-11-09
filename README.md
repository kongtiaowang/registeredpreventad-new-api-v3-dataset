# PREVENT-AD registered dataset — rebuilt against the new LORIS API (v0.0.3)

This repository mirrors `conpdatasets/preventad-registered`: identical file
paths and identical git-annex MD5E keys.  Only the download URLs were rewritten
to the new instance:

```
https://registeredpreventad-new.loris.ca/api/v0.0.3/projects/PREVENT-AD/...
```

## Structure

```
clinical-data/          120 data-release files (18 from 2020 + newer versions)
images-in-BIDS/BIDS_dataset/   60,184 files (BIDS)
images-in-MINC/         32,255 files (MINC + candidate/visit/handedness JSON)
```

## Getting the data

The API requires a LORIS token.  After cloning (content is not included):

```bash
git clone https://github.com/kongtiaowang/registeredpreventad-new-api-v3-dataset.git
cd registeredpreventad-new-api-v3-dataset

TOKEN=$(curl -s -X POST -H 'Content-Type: application/json' \
  -d '{"username":"<user>","password":"<password>"}' \
  https://registeredpreventad-new.loris.ca/api/v0.0.3/login \
  | python3 -c 'import json,sys;print(json.load(sys.stdin)["token"])')

git config annex.http-headers "Authorization: Bearer $TOKEN"
git annex get .        # or: datalad get .
```

Tokens expire; refresh the config value when downloads start returning
`Unauthorized`.

## Known differences from the 2020 dataset

* `sub-5443870` no longer exists in the new database (58 files).
* `participants.tsv` was updated upstream (content differs from the crawl).
* 3,462 MINC metadata files (`candidate.json`, `visit.json`,
  `handedness.json`) keep their original keys but have no download URL:
  the API does not serve byte-identical versions of those generated files.
  Everything else has a working URL.

## Regenerated metadata (v3)

`images-in-MINC/**/candidate.json` and `visit.json` (1,900 files) were
regenerated from the current API and stored as regular git files.  The 2020
versions had different content and cannot be re-fetched byte-identically, so
their old annex keys were dropped for these files only.

`sub-5443870` was removed: it no longer exists in the new database.
`participants.tsv`, `dataset_description.json`, `README`, `participants.json`
and `.bids-validator-config.json` were refreshed from the API.
