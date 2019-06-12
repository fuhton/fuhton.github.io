---
layout: post
title: Migrate from Flow to Typescript
---

Whatever your reason for migration from Flow to Typescript, it's not easy - especially for a mature codebase with active contributors. Speaking from experience, this will cause headaches for you and your team. Personally I took 2 different approaches, the latter being successful (mostly). My first approach was to have Flow and Typescript coexist in a 1,000 file project, but this really doesn't work for two big reasons 

1) Developer experience - devs have to know 2 typing systems and which typing system to apply to their current work
2) Project setup - your project now needs to support both typing systems and _exclude_ the other typing system in the correct files - aka a headache

Taking a step back, I realized the best way is to rip the bandaid off and migrate over a long weekend on your own (depending on the size/shape of your team). I started on a Friday morning and finish up on a Sunday evening - by Monday morning I had our entire CI/CD process running smoothly and my team quickly reviewed and approved the changes. It took a few bad starts to get everything just right (so that I could get it done in a weekend) so I've decided to share my approach so you can avoid similar mistakes.

> Best solution: Swap type systems in one go.

## Expectations
* Expect to have lots of `any` scattered in your code base. When doing the migration, your priority should be getting the code healthy, CI/CD passing, and then merged. 
* Expect to have a ton of issues at first - as you resolve massive changes (`*` to `any`) you'll see fewer and fewer issues until all that is left is ~200-500 real issues. Those can again be waved away through smart use of search/replace in your IDE.
* Expect to have some build issues. Your pipeline is changing and there will be some issues with building the project for production.

## Helpful Prep
I wish I had done a few steps in preparation for this change. The first was luckily already in place, the other two I learned through trial-error.

1) Switch to babel as a build tool

Having babel as a build tool resolves all sorts of issues when turning your JavaScript into TypeScript. It'll also solve a lot of headache if you do any type of module-resolving. 

2) Get up-to-date with Babel 7

I hot-swapped Babel 7 in during my migration and it broke most existing functionality. I'd encourage a small PR before you do the swap to stave off this issue.

3) Do a quick pass and figure our your tsconfig.json and tslint.json file

Figuring this out beforehand will save you valuable time when you're trying to get this migration done. 

## CLI Commands
#### React Projects
The below script will search for any file with `/>` within the `src` directory. We assume that this notation means that there is JSX in that file. It's not a catch all and you could swap out for `/>` for `</` if you have some unique files.
````bash
echo -e "grep -riRl --include=\*.ts \"/>\" src | while read FILE ; do\n    echo $FILE;\n    mv -v \"$FILE\" \"${FILE/.ts/.tsx}\";\ndone" >> flow-to-ts.sh
./flow-to-ts.sh
````

#### All Projects
You can run a variant of all projects

````bash
echo -e "grep -riRl --include=\*.js \"\" src | while read FILE ; do\n    echo $FILE;\n    mv -v \"$FILE\" \"${FILE/.js/.ts}\";\ndone" >> flow-to-ts.sh
./flow-to-ts.sh
````

#### Node Commands
I always recommend making a `npm script` command (or yarn), so I'll assume that you know how to accomplish this. If you're not interested in polluting your package.json file - you can run `./node_modules/.bin/<cli tool>`.

* `tsc --project tsconfig.json`
	* Run the typescript parser on all files.
* `tslint -p tsconfig.json --fix`
	* Run tslint for all typescript files and fix anything it can fix
* `prettier --parser typescript --write 'src/**/*.ts'`
	* Run prettier on all typescript files

When doing the migration, I recommend running the commands in this order many times. This led me to the most success as changing things to satisfy the linter might cause errors in the `tsc` command. Prettier fixes might cause issues with either command. This cascades until you can safely resolve the issue.

## My Files
#### tsconfig.json
````
{
	"compilerOptions": {
		"baseUrl": "./",
		"esModuleInterop": true,
		"incremental": true,
		"jsx": "react",
		"module": "commonjs",
		"outDir": "public",
		"paths": {
			// module-resolvers
		},
		"resolveJsonModule": true,
		"sourceMap": true,
		"target": "esnext"
	},
	"include": [
		"src/**/*"
	],
	"exclude": [
		"node_modules",
	]
}
````

#### tslint.json
````
{
	"extends": ["tslint:recommended", "tslint-react", "tslint-config-prettier"],
	"linterOptions": {
		"exclude": ["node_modules"]
	},
	"rules": {
		// Rules should be project specific
	}
}
````

#### babel.config.js
````
module.exports  = {
	presets: [
		'@babel/preset-env',
		'@babel/preset-react',
		'@babel/preset-typescript',
	],
	plugins: [
		// project specific
	],
};
````

## Most important
[This repo](https://github.com/bcherny/flow-to-typescript#typescript-vs-flow) gives a great overview of the differences in Flow vs Typescript. I copy-pasted many search/replaces directly from this repo with much success. 

Hopefully everything here makes sense. As always, reach out to me on  [twitter](https://twitter.com/fuhton)  if you have any questions.
