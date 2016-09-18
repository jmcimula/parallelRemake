The first submission of `parallelRemake` to CRAN is pending. 

When I tested via `R CMD CHECK --as-cran` in R 3.3.1 on Mac OS 10.11.6, and there were no warnings or errors. The only note was the following.

```
* checking CRAN incoming feasibility ... NOTE
Maintainer: ‘Landau William <will.landau@gmail.com>’

New submission

Unknown, possibly mis-spelled, fields in DESCRIPTION:
  ‘Remotes’

Strong dependencies not in mainstream repositories:
  remake
```

The `remake` dependency message will disappear when [remake](https://github.com/richfitz/remake) appears on CRAN (which could be a long while yet). At that time, I will remove the `Remotes` field in the DESCRIPTION, and the spelling note will disappear.

Builds passed successfully on [AppVeyor](https://www.appveyor.com/) and [Travis CI](https://travis-ci.org/). The latest build status is displayed with the badges at the top of the [README](https://github.com/wlandau/parallelRemake/blob/master/README.md).