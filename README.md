# 31075

git@github.com:Enkidu-Aururu/31075.git# minimal-reproduction-template

Based on [Renovate minimal reproduction instructions](https://github.com/renovatebot/renovate/blob/main/docs/development/minimal-reproductions.md).

## Problem

We use for private python packages stored in [google artifact registry](https://cloud.google.com/artifact-registry/docs/python/store-python), which is also private.

Our issue is missing Renovate **sourceUrl** visible as link in Package column from Gitlab merge request.

Originally, I thought it might be caused by a fact that our packages were built the old fashioned way by:

```python
from setuptools import setup

setup(
    #...
    url="https://github.com/Enkidu-Aururu/31075",
    #...
)
```

I thought so because in this way, we do not define [project_urls keyword](https://setuptools.pypa.io/en/latest/references/keywords.html),
which is mentioned in the [Renovates pypi datasource](https://github.com/renovatebot/renovate/discussions/31075).

But when I did try to reproduce this behavior with the local **setup.py** and by the following commands for the public PYPI,
I was not successful.
```bash
python3.12 -m venv venv
. venv/bin/activate
grep -v "renovate-" requirements.txt | xargs -i pip install {}
python3.12 -m build
twine upload dist/* --skip-existing
```

As you can see in the [public PYPI API](https://pypi.org/pypi/renovate-31075-sample/0.0.1/json), 
resulting package translated **url** keyword into both json keys:

```json
{
  "info": {
    "home_page": "https://github.com/Enkidu-Aururu/31075",
    "project_urls": {
      "Homepage": "https://github.com/Enkidu-Aururu/31075"
    }
  }
}
```

And Renovate correctly detected this URL in an [update PR](https://github.com/Enkidu-Aururu/31075/pull/1) -
see the **Package** column link.

Hence, this led me to conclusion that **sourceUrl** might be somehow missing only when we use
[google artifact registry](https://cloud.google.com/artifact-registry/docs/python/store-python) as private PYPI.

I tried and failed to setup example with python package in the google registry (struggling with service acount access in my demo account).
And since I am not willing to spend more time on this, we can probably close this discussion.

## Expectation

If possible, maybe find out why Renovate does not extract **sourceUrl** for python packages stored in google artifact registry.
In case it is some sort of limitation caused by simple pypi api, maybe document it.

## References

[Renovate discussion #31075](https://github.com/renovatebot/renovate/discussions/31075 "Could PYPI datasource accept also look into url field?")
