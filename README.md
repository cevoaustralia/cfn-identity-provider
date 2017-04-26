# Preparing metadata

1. Download metadata from your IdP
1. Make it all be on one line: `tr -d '\n' metadata.xml | sed -e 's/"/\"/g' > out.xml`
1. Copy the `out.xml` into the `ParameterValue` field of the `params.json`


