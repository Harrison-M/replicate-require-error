Bizarre dependency conflict error
=================================

How to replicate
----------------

Run `node index.js`.  Leave the node_modules folders as they stand.  You should receive this error:

```
/Users/harrison/Projects/replicate-require-error/node_modules/dependency/node_modules/moment-timezone/index.js:2
moment.tz.load(require('./data/packed/latest.json'));
          ^
TypeError: Object function () {
			var args = [], i, len = arguments.length - 1;
			for (i = 0; i < len; i++) {
				args[i] = arguments[i];
			}
			var m = moment.apply(null, args);
			var preTzOffset = m.zone();
			m.tz(arguments[len]);
			return m.add('minutes', m.zone() - preTzOffset);
		} has no method 'load'
    at Object.<anonymous> (/Users/harrison/Projects/replicate-require-error/node_modules/dependency/node_modules/moment-timezone/index.js:2:11)
    at Module._compile (module.js:456:26)
    at Object.Module._extensions..js (module.js:474:10)
    at Module.load (module.js:356:32)
    at Function.Module._load (module.js:312:12)
    at Module.require (module.js:364:17)
    at require (module.js:380:17)
    at Object.<anonymous> (/Users/harrison/Projects/replicate-require-error/node_modules/dependency/lib/helpers.js:1:76)
    at Module._compile (module.js:456:26)
    at Object.Module._extensions..js (module.js:474:10)
```

`npm ls` output:
----------------

```
/Users/harrison/Projects/replicate-require-error
├─┬ dependency@1.0.0
│ └── moment-timezone@0.1.0
├── moment@2.6.0
└── moment-timezone@0.0.6
```

Analysis
--------

What appears to happen is when index.js in moment-timezone@0.1.0 requires `./moment-timezone`, it receives `moment-timezone.js` from 0.0.6 at the base of the tree, not 0.1.0, where it lives.

Note that if you were to remove `dependency` and instead include it via npm link, the error would not occur.  Likewise, the error does not occur if dependency is required before moment-timezone, as shown by running `node works.js`.

I have tested this in node 0.10.29, the latest stable version as of this writing.  I haven't tested it in any of the 0.11.x development versions yet.
