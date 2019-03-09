# **REACT**
Some REACT useful codes:

## Summary

* [Make react accept JSX extensions](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/REACT/codes.md#make-react-accept-jsx-extensions)
* [Bundle Android App Without Device](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/REACT/codes.md#bundle-android-app-without-device)

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

### Bundle Android App Without Device
##### Bundle the Android Version of the App with no need to connect a device in your PC

```BASH
react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/
```

----------
