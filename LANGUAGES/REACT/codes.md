# **REACT**
Some REACT useful codes:

## Summary

* [Make react accept JSX extensions](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/REACT/codes.md#make-react-accept-jsx-extensions)

----------

### Make react accept JSX extensions
##### The last version of react (16.8 at this moment) do not accept jsx extensions when buildind a react-native app, so you have to create a `rn-cli.config.js` at the root of the project with this code inside:

```javascript
module.exports = {
    resolver: {
        sourceExts: ['js', 'json', 'ts', 'tsx', 'jsx']
    }
}
```

----------