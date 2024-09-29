# protonmail-bridge

## Upstream pre-releases

Some upstream releases are tagged as "Pre-release" in the GitHub upstream repository and should not be packaged (as they are not considered "stable" by upstream).  
See, for instance: <https://github.com/ProtonMail/proton-bridge/releases/tag/v3.13.0>

Make sure to verify that new upstream releases are not tagged as "Pre-release" before packaging them.  
Note that such "pre-releases" are filtered out in the nvchecker integration (as it specifically looks for releases tagged as "latest").
