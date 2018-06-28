# Problem:
`npm run dev` gives `(node:xxxx) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): SyntaxError: Unexpected end of JSON input`
# Solution:
Clearing the build cache for hard source plugin at `node_modules/.cache/hard-source/`