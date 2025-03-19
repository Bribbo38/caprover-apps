## Custom Repository for Caprover OneClick Apps

### How to create a one-click app:

- Find/create a docker-compose file for the app you're interested in.
- Add `captainVersion: 4` to the very top of the yaml file.
- Add this section to the end of the yaml file:
```yaml
caproverOneClickApp:
    variables:
        - id: '$$variable_one'
          label: Variable One
          defaultValue: 'value'
          description: The description for the variable
          validRegex: '/A-Za-z/'
    instructions:
        start: |-
            A description that will be displayed to the user when they
            are installing one click app!
            It can be multiline.
        end: |-
            A summary when the app is deployed!
            It can be multiline.
            It can also include the dynamic parameters such as
            $$variable_one
    displayName: The display name of the App
    isOfficial: true ## Only if all images used here are official or from a trusted source.
    description: A 200 less description of the app
    documentation: A reference to the documentation of the app
```

### Variables:
- Variables are prefixed with `$$cap`
- Variables can be anywhere in the content and they will be replaced by what user enters
- There are 3 special variables that are built-in for all oneclick apps: `$$cap_appname`, `$$cap_root_domain`, and `$$cap_gen_random_hex(length)`. For example, if your app needs environment variables with the URL value of the app, you can use `$$cap_appname.$$cap_root_domain` which resolves to something like `myappname.rootdomain.com`. Also If you need a default password, you can use `$$cap_gen_random_hex(10)`
- Each custom variable must have `id`, `label`. They could also have `defaultValue`, `validRegex`, `description`.
- IMPORTANT: by default, fields are not required to be filled. If validRegex is not set, the field can be set as empty and ignored by the user.


### Services:
Even though, the format used by One Click apps is Docker Compose, not all parameters defined in Docker Compose file are parsed out by CapRover. Only the following parameters are used:
- `image`
- `environment`
- `ports`
- `volumes`
- `depends_on`
- `hostname`
- `command`
- `cap_add`

(_other parameters will be ingored_)

Aside the the Docker Compose template, services have a special subsection specific to CapRover called `caproverExtra` which contains service specific parameters that are only available via CapRover and not docker compose. Currently this field can take the following variables:
- `dockerfileLines` which is a multiline variable, and can be used instead of `image` property in the service. You must delete the `image` property if you want to use this parameter. NOTE: `dockerfileLines` should be used in rare cases where other methods cannot work. Prefer `command` where possible. 
- `containerHttpPort` is useful when the underlying service uses a custom port for HTTP. If not provided, the default will be `"80"`
- `notExposeAsWebApp` can be set to `"true"` when the underlying service is not an HTTP app. This is useful for databases and other internally used services.
- `websocketSupport` can be set to `"true"` to automatically enable Websocket Support. Only supported in versions 1.12+

### Icon
- Make sure you add an app icon to the logos directory!


---------

## Build your own one-click app repository
You may want to build your own private repository. CapRover supports having multiple repositories. You can add new repository URLs to the one click app page. The official one, this one, is available as `https://oneclickapps.caprover.com`.

To create your own repository:
- Fork this repository
- Delete all existing apps (to avoid duplicate apps), and add your own apps.
- Run `npm i`
- Run `npm run validate_apps`
- Run `npm run formatter-write`
- Run `npm run build`
- Now you can host the static content placed in `./dist` directory anywhere you want, the official repo uses [github pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) to publish the content. Make sure to update [CNAME](https://github.com/caprover/one-click-apps/blob/master/public/CNAME) to your own URL if you decide to do so.