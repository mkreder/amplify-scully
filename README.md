# amplify-scully

Docker image to run [Scully](https://github.com/scullyio/scully) on AWS Amplify or AWS CodeBuild/CodePipeline.

## Scully configuration

Your scully.*.config.ts should have defined these puppeteerLaunchOptions to avoid running Chrome in sandbox mode:

```
export const config: ScullyConfig = {
  projectRoot: "./src",
  projectName: "yourproject",
  outDir: './dist/static',
  puppeteerLaunchOptions: {
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  },
  routes: {
  }
};
```

## AWS Amplify

For AWS Amplify, you should use an amplify.yml file like this:

```
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - yarn install
    build:
      commands:
        - yarn run build --prod
        - yarn run scully 
  artifacts:
    baseDirectory: dist/static
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```


## CodeBuild / CodePipeline

For CodeBuild your buildspec.yml should look like this:

```
version: 0.2

phases:
  pre_build:
    commands:
        - yarn install
  build:
    commands:
        - yarn run build --prod
        - yarn run scully --serverTimeout=180
artifacts:
    files:
        - '**/*'
    base-directory: 'dist/static'
```