### Using LoDash

A drop in replacement for underscore

## Installation

```bash
# First make sure npm is updated
npm install -g npm

# Then install LoDash
npm install -g lodash-cli
```

## Running LoDash

```bash
# Create a full drop-in-replace for underscore
lodash underscore --output '/sites/ycharts/apps/main/static/main/js/vendors/lodash-2.4.1.underscore.js'

# Current global version using our subset of _.underscore methods in all of YCharts frontend
sudo lodash include='all,each,extend,intersection,isString,isFunction,isElement,once,sortBy,uniqueId,zip' --output '/sites/ycharts/apps/main/static/main/js/vendors/lodash-2.4.1.ycharts-global.js'
```
