# everyday_learn_Sum00n
## localizations

> Add dependency on pubspec.yaml:
```dart
dependencies:
  easy_localization: ^3.0.0
```

> Create folder in project

```dart
assets
└── translations
    ├── en.json                  
    └── bn.json    
```
> Update pubspec.yaml: 

```dart
flutter:
  assets:
    - assets/translations/
```

> Download i18n manager

[Download Windows Version](https://www.electronjs.org/apps/i18n-manager)

> Install it and open the translation file into it.Right click in unkhown prefix and add item.

![Untitled11](https://user-images.githubusercontent.com/96842264/156578052-aa1c690e-fc9f-4ebc-934c-ed7441ecfd10.png)


> Go to main.dart add those line in main method

```dart
  WidgetsFlutterBinding.ensureInitialized();
  await EasyLocalization.ensureInitialized();
  runApp(
    EasyLocalization(
        path: 'assets/translations',
        supportedLocales: const [
          Locale('en'),
          Locale('bn'),
        ],
        fallbackLocale: const Locale('en'),
        child: const MyApp()),
  );
```

> Go to materialApp in MyApp() add those line 

```dart
localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
```
> Generate codegen_loader.g.dart in directory lib/translations/ by running this command in terminal

```dart
flutter pub run easy_localization:generate -h

flutter pub run easy_localization:generate -S "assets/translations" -O "lib/translations"

flutter pub run easy_localization:generate -S "assets/translations" -O "lib/translations" -o "locale_keys.g.dart" -f keys
```

> Update runapp via add

```dart
assetLoader: const CodegenLoader(),
```
> Now use any string in app from json file  via

```dart
             import 'package:easy_localization/easy_localization.dart'; for use tr()
              LocaleKeys.nameThatYouGenerateIni18nManager.tr(),
```
> For change the language use

```dart
context.setLocale(const Locale('en'),);
```
