Варіант #1. Тільки Single Device:

1. Sign Up: `eThree.bootstrap()`

```js
import { EThree } from '@virgilsecurity/e3kit';
// Once user is authenticated, bootstrap e3kit SDK to load
// his private key
async function onUserRegistered(user) {
    const getToken = () => fetchToken(user.token);
    // Initialize e3kit - see full sample in EThree.initialize page
	const eThree = await EThree.initialize(getToken);

    // Bootstrap user (i.e. check existence of local private key and
    // create private key if user with this identity doesn't exists yet)
    eThree.bootstrap();

    // User private key loaded, ready to end-to-end encrypt!
});
```

2. Sign In (на подальший логін)

```js
// нічого не треба просто юзайте encrypt/decrypt
```

Варіант #2. Тільки Multi Device:

1. Sign Up: `eThree.bootstrap(pwd)`

```js
import { EThree } from '@virgilsecurity/e3kit';
// Once user is authenticated, bootstrap e3kit SDK to load
// his private key

async function onUserRegistered(user) {
    const getToken = () => fetchToken(user.token);
    // Initialize e3kit - see full sample in EThree.initialize page
	const eThree = await EThree.initialize(getToken);

    // Bootstrap user (i.e. check existence of local private key and
    // create private if user with this identity doesn't exist yet)
    eThree.bootstrap(user.password);

    // User private key loaded, ready to end-to-end encrypt!
});
```

2. Sign In (На подальший логін, той же девайс)

```js
// нічого не треба просто юзайте encrypt/decrypt
```

3. На кожному новому дивайсі викликаємо

а) якщо точно ви знаєте що це новий дивайс для користувача, то викликаємо `eThree.bootstrap(pwd)` на Sign In:

```js
import { EThree } from '@virgilsecurity/e3kit';
// Once user is authenticated, bootstrap e3kit SDK to load
// his private key

async function onUserRegistered(user) {
    const getToken = () => fetchToken(user.token);
    // Initialize e3kit - see full sample in EThree.initialize page
	const eThree = await EThree.initialize(getToken);

    // Bootstrap user (i.e. check existence of local private key and
    // create card if user with this identity doesn't exist yet)
    eThree.bootstrap(user.password);

    // User private key loaded, ready to end-to-end encrypt!
});
```
в подальшому на новому дивайсі `eThree.bootstrap(pwd)` викликати не потрібно.


б) якщо ви НЕ трекаєте, що це новий дивайс користувача, то викликайте на Sign IN:

```js
    function onLogin() {
            try {
                await eThree.bootstrap();
            } catch (e) {
         // if new Device
                await eThree.bootstrap("pwd");
            }
    }

    // User private key loaded, ready to end-to-end encrypt!
});

Варіант #3. Enable Multi Device (Migrate from single to multi)

1. На першому SINGLE DEVICE викликаємо `eThree.backupPrivateKey(pwd)`

```js
// TODO: init and bootstrap user (see EThree.initialize and EThree.bootstrap)

// If user wants to delete her account, this is the function to
//	delete her private key
eThree.resetPrivateKeyBackup(encryptionPassword)


2. На кожному новому дивайсі викликаємо

а) якщо точно ви знаєте що це новий дивайс для користувача, то викликаємо `eThree.bootstrap(pwd)` на Sign In:

```js
import { EThree } from '@virgilsecurity/e3kit';
// Once user is authenticated, bootstrap e3kit SDK to load
// his private key

async function onUserRegistered(user) {
    const getToken = () => fetchToken(user.token);
    // Initialize e3kit - see full sample in EThree.initialize page
	const eThree = await EThree.initialize(getToken);

    // Bootstrap user (i.e. check existence of local private key and
    // create card if user with this identity doesn't exist yet)
    eThree.bootstrap(user.password);

    // User private key loaded, ready to end-to-end encrypt!
});
```
в подальшому на новому дивайсі `eThree.bootstrap(pwd)` викликати не потрібно.


б) якщо ви НЕ трекаєте, що це новий дивайс користувача, то викликайте на Sign IN:

```js
    function onLogin() {
            try {
                await eThree.bootstrap();
            } catch (e) {
         // if new Device
                await eThree.bootstrap("pwd");
            }
    }

    // User private key loaded, ready to end-to-end encrypt!
});
