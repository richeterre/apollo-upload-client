![Apollo upload logo](https://cdn.rawgit.com/richeterre/apollo-upload-client/27b1f20/apollo-upload-logo.svg)

# apollo-upload-client

**This fork modifies the request format to support Absinthe's way of handling file uploads.**

Enhances [Apollo](https://apollographql.com) for intuitive file uploads via GraphQL queries or mutations. Use with the [Absinthe](https://absinthe-graphql.org) library for Elixir.

## Setup

Install with peer dependencies using [npm](https://npmjs.com):

```shell
npm install @richeterre/apollo-upload-client apollo-link
```

Initialize Apollo Client with this terminating link:

```js
import { createUploadLink } from '@richeterre/apollo-upload-client'

const link = createUploadLink(/* Options */)
```

### Options

`createUploadLink` options match [`createHttpLink` options](https://www.apollographql.com/docs/link/links/http.html#Options):

* `includeExtensions` (boolean): Toggles sending `extensions` fields to the GraphQL server. (default: `false`).
* `uri` (string): GraphQL endpoint URI (default: `/graphql`).
* `credentials` (string): Overrides `fetchOptions.credentials`.
* `headers` (object): Merges with and overrides `fetchOptions.headers`.
* `fetchOptions` (object): [`fetch` init](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters); overridden by upload requirements.
* `fetch` (function): [Fetch API](https://fetch.spec.whatwg.org) to use (default: Global `fetch`).

## Usage

Use [`FileList`](https://developer.mozilla.org/en/docs/Web/API/FileList), [`File`](https://developer.mozilla.org/en/docs/Web/API/File), [`Blob`](https://developer.mozilla.org/en/docs/Web/API/Blob) or [`ReactNativeFile`](#react-native) instances anywhere within query or mutation input variables, as you would with strings, integers and other types.

### [`FileList`](https://developer.mozilla.org/en/docs/Web/API/FileList)

```jsx
import gql from 'graphql-tag'
import { graphql } from 'react-apollo'

export default graphql(gql`
  mutation($files: [Upload!]!) {
    uploadFiles(files: $files) {
      id
    }
  }
`)(({ mutate }) => (
  <input
    type="file"
    multiple
    required
    onChange={({ target: { validity, files } }) =>
      validity.valid && mutate({ variables: { files } })
    }
  />
))
```

### [`File`](https://developer.mozilla.org/en/docs/Web/API/File)

```jsx
import gql from 'graphql-tag'
import { graphql } from 'react-apollo'

export default graphql(gql`
  mutation($file: Upload!) {
    uploadFile(file: $file) {
      id
    }
  }
`)(({ mutate }) => (
  <input
    type="file"
    required
    onChange={({ target: { validity, files: [file] } }) =>
      validity.valid && mutate({ variables: { file } })
    }
  />
))
```

### [`Blob`](https://developer.mozilla.org/en/docs/Web/API/Blob)

```jsx
import gql from 'graphql-tag'

// Apollo Client instance
import client from './apollo'

const file = new Blob(['Foo.'], { type: 'text/plain' })

// Optional, defaults to `blob`
file.name = 'bar.txt'

client.mutate({
  mutation: gql`
    mutation($file: Upload!) {
      uploadFile(file: $file) {
        id
      }
    }
  `,
  variables: { file }
})
```

### React Native

Substitute [`File`](https://developer.mozilla.org/en/docs/Web/API/File) with `ReactNativeFile` from [`extract-files`](https://github.com/jaydenseric/extract-files):

```js
import { ReactNativeFile } from '@richeterre/apollo-upload-client'

const file = new ReactNativeFile({
  uri: '…',
  type: 'image/jpeg',
  name: 'photo.jpg'
})

const files = ReactNativeFile.list([
  {
    uri: '…',
    type: 'image/jpeg',
    name: 'photo-1.jpg'
  },
  {
    uri: '…',
    type: 'image/jpeg',
    name: 'photo-2.jpg'
  }
])
```

## Support

* Node.js v6.10+, see `package.json` `engines`.
* [Browsers >1% usage](http://browserl.ist/?q=%3E1%25), see `package.json` `browserslist`.
* React Native.
