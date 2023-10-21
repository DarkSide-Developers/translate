# @darkside-developers/translate


<p align="center">
  <a href="" rel="noopener">
 <img width=180px height=180px src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/Google_Translate_logo.svg/512px-Google_Translate_logo.svg.png?20210606111727" alt="@darkside-developers/translate"></a>
</p>

[![npm](https://img.shields.io/npm/v/@darkside-developers/translate)](https://www.npmjs.com/package/@darkside-developers/translate)
[![npm](https://img.shields.io/npm/dt/@darkside-developers/translate)](https://www.npmjs.com/package/@darkside-developers/translate)


A Node.js library for fetching Google translate result.

## Installation

```bash
npm install @darkside-developers/translate
```

## Usage

From automatic language detection to English:

```ts
const { translate } = require('@darkside-developers/translate');

translate('මම ඔයාට ආදරෙයි', {to: 'en'}).then(res => {
    console.log(res.text);
    //=> I Love You
}).catch(err => {
    console.error(err);
});
```

> Please note that maximum text length for single translation call is **5000** characters. 

From English to Dutch with a type:

```ts
translate('I spea Dutch!', {from: 'en', to: 'nl'}).then(res => {
    console.log(res.text);
    //=> Ik spreek Nederlands!
    console.log(res.from.text.autoCorrected);
    //=> true
    console.log(res.from.text.value);
    //=> I [speak] Dutch!
    console.log(res.from.text.didYouMean);
    //=> false
}).catch(err => {
    console.error(err);
});
```

>> Sometimes, the API will not use the auto corrected text in the translation:

```ts
translate('I spea Dutch!', {from: 'en', to: 'nl'}).then(res => {
    console.log(res);
    console.log(res.text);
    //=> Ik spea Nederlands!
    console.log(res.from.text.autoCorrected);
    //=> false
    console.log(res.from.text.value);
    //=> I [speak] Dutch!
    console.log(res.from.text.didYouMean);
    //=> true
}).catch(err => {
    console.error(err);
});
```

You can also add languages in the code and use them in the translation:

```ts
translate = require('google-translate-api');
translate.languages['sr-Latn'] = 'Serbian Latin';

translate('translator', {to: 'sr-Latn'}).then(res => ...);
```

## Proxy
Google Translate has request limits. If too many requests are made, you can either end up with a 429 or a 503 error.
You can use **proxy** to bypass them:
```ts
const tunnel = require('tunnel');
translate('Ik spreek Engels', {to: 'en'}, {
    agent: tunnel.httpsOverHttp({
    proxy: { 
      host: 'whateverhost',
      proxyAuth: 'user:pass',
      port: '8080',
      headers: {
        'User-Agent': 'Node'
      }
    }
  }
)}).then(res => {
    // do something
}).catch(err => {
    console.error(err);
});
```

## Does it work from web page context?
No. `https://translate.google.com` does not provide [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) http headers allowing access from other domains.

## API

### translate(text, [options], [gotOptions])

#### text

Type: `string`

The text to be translated

#### options

Type: `object`

##### from
Type: `string` Default: `auto`

The `text` language. Must be `auto` or one of the codes/names (not case sensitive) contained in [languages.js](https://github.com/DarkSide-Developers/translate/blob/master/data/languages.js)

##### to
Type: `string` Default: `en`

The language in which the text should be translated. Must be one of the codes/names (case sensitive!) contained in [languages.js](https://github.com/DarkSide-Developers/translate/blob/master/data/languages.js).

##### raw
Type: `boolean` Default: `false`

If `true`, the returned object will have a `raw` property with the raw response (`string`) from Google Translate.

##### client
Type: `string` Default: `"t"`

Query parameter `client` used in API calls. Can be `t|gtx`.

##### tld
Type: `string` Default: `"com"`

TLD for Google translate host to be used in API calls: `https://translate.google.{tld}`.

#### gotOptions
Type: `object`

The got options: https://github.com/sindresorhus/got#options

### Returns an `object`:
- `text` *(string)* – The translated text.
- `from` *(object)*
  - `language` *(object)*
    - `didYouMean` *(boolean)* - `true` if the API suggest a correction in the source language
    - `iso` *(string)* - The [code of the language](https://github.com/DarkSide-Developers/translate/blob/master/data/languages.js) that the API has recognized in the `text`
  - `text` *(object)*
    - `autoCorrected` *(boolean)* – `true` if the API has auto corrected the `text`
    - `value` *(string)* – The auto corrected `text` or the `text` with suggested corrections
    - `didYouMean` *(boolean)* – `true` if the API has suggested corrections to the `text`
- `raw` *(string)* - If `options.raw` is true, the raw response from Google Translate servers. Otherwise, `''`.

Note that `res.from.text` will only be returned if `from.text.autoCorrected` or `from.text.didYouMean` equals to `true`. In this case, it will have the corrections delimited with brackets (`[ ]`):

```ts
translate('I spea Dutch').then(res => {
    console.log(res.from.text.value);
    //=> I [speak] Dutch
}).catch(err => {
    console.error(err);
});
```
Otherwise, it will be an empty `string` (`''`).

## Author

- **Dark_Winzo**
  - GitHub: [DarkWinzo](https://github.com/DarkWinzo)
  - Email: darkwinzo2240@gmail.com (optional)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Feel free to open an issue or create a pull request for any improvements or bug fixes.

## Versioning

We use Semantic Versioning for versioning. For the versions available, see the tags on this repository.

## Contact

For any questions or feedback, feel free to reach out to the project creator:

- Name: DarkWinzo
- GitHub: [@DarkWinzo](https://github.com/DarkWinzo)

