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
//use this command for making locale_keys.g.dart for avoid typing mistake
flutter pub run easy_localization:generate -S "assets/translations" -O "lib/translations" -o "locale_keys.g.dart" -f keys

```

> Update runapp via add

```dart
assetLoader: const CodegenLoader(),
```
> Now use any string in app from json file  via

```dart
             import 'package:easy_localization/easy_localization.dart'; for use tr()
              Text(
              Localkeys.hi_text.tr(),
            ),
```
> For change the language use

```dart
context.setLocale(const Locale('en'),);
```


## hydrated bloc
> An extension to package:bloc which automatically persists and restores bloc and cubit states

> First add dependency

```dart
  hydrated_bloc: ^8.0.0
  path_provider: ^2.0.9
  flutter_bloc: ^8.0.1
  freezed_annotation: ^1.1.0
  injectable: ^1.5.3
  get_it: ^7.2.0
```
> First add dev dependency

```dart
  build_runner: null
  freezed: ^1.1.1
  injectable_generator: ^1.5.3
```
> In main.dart
> hydrated_bloc exports a Storage interface which means it can work with any storage provider. Out of the box, it comes with its own implementation: HydratedStorage.

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  final storage = await HydratedStorage.build(
    storageDirectory: await getTemporaryDirectory(),
  );
  configureInjection(Environment.prod);
  HydratedBlocOverrides.runZoned(
    () => runApp(App()),
    storage: storage,
  );
}
```

> make a hydratedCubit which can store a state of your app

```dart
@injectable
class LanguageCubit extends HydratedCubit<bool> {
  LanguageCubit() : super(false);

  void toggleTheme({required bool value}) {
    emit(value);
  }

  @override
  bool? fromJson(Map<String, dynamic> json) {
    return json['isBangla'] as bool;
  }

  @override
  Map<String, dynamic>? toJson(bool state) {
    // ignore: unnecessary_cast
    return {
      'isBangla': state,
    } as Map<String, dynamic>;
  }
}
```
> now provide this cubit via getIt

```dart
//run this command for generate the injectaable getIt
flutter pub run build_runner build --delete-conflicting-outputs
```
```dart
BlocProvider(
      create: (context) => getIt<LanguageCubit>(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        localizationsDelegates: context.localizationDelegates,
        supportedLocales: context.supportedLocales,
        locale: context.locale,
        title: LocaleKeys.title_text.tr(),
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: MyHomePage(title: LocaleKeys.title_text.tr()),
      ),
    );
```
> now use this BlocProvider in MyHomePage for changing the app state and store it

```dart
BlocBuilder<LanguageCubit, bool>(
      builder: (context, state) {
        dropdownValue = state ? 'বাংলা' : 'Eng';
        return Scaffold(
          appBar: AppBar(
            title: Center(child: Text(LocaleKeys.title_text.tr())),
            actions: [
              Padding(
                padding: const EdgeInsets.all(8.0),
                child: DropdownButton<String>(
                  value: dropdownValue,
                  icon: const Icon(Icons.change_circle),
                  elevation: 16,
                  style: const TextStyle(color: Colors.white),
                  underline: Container(
                    height: 0,
                    color: Colors.transparent,
                  ),
                  onChanged: (String? newValue) {
                    dropdownValue = newValue;
                    BlocProvider.of<LanguageCubit>(context)
                        .toggleTheme(value: !state);

                    state
                        ? context.setLocale(const Locale('en'))
                        : context.setLocale(const Locale('bn'));
                  },
                  items: <String>[
                    'Eng',
                    'বাংলা',
                  ].map<DropdownMenuItem<String>>(
                    (String value) {
                      return DropdownMenuItem<String>(
                        value: value,
                        child: Text(
                          value,
                          style: const TextStyle(
                            color: Colors.black,
                          ),
                        ),
                      );
                    },
                  ).toList(),
                ),
              ),
            ],
          ),
          body: Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Text(
                  //tr() is for import 'package:easy_localization/easy_localization.dart';
                  LocaleKeys.hi_text.tr(),
                ),
                Text(
                  LocaleKeys.welcome_text.tr(),
                ),
                const Text(LocaleKeys.money).plural(
                  1000000,
                  format: NumberFormat.compact(
                    locale: context.locale.toString(),
                  ),
                ),
                const Text(LocaleKeys.day).plural(21),
                const Text(LocaleKeys.money).plural(10.23),
                const Text(LocaleKeys.money_args)
                    .plural(10.23, args: ['sumon', '10.23']),
                const Text('Linked translations:topics add two string'),
                Text(
                  state
                      ? BanglaUtility.getBanglaDate(
                          day: DateTime.now().day,
                          month: DateTime.now().month,
                          year: DateTime.now().year,
                        )
                      : LocaleKeys.dateLogging.tr(
                          namedArgs: {
                            'currentDate': DateTime.now().toLocal().toString(),
                          },
                        ),
                ),
              ],
            ),
          ),
        );
      },
    );
  }
}
```
