# ライブラリ

この章は、どのようにあなたのライブラリをComposerでインストールできるようにするかお話します。

<!--
This chapter will tell you how to make your library installable through Composer.
-->

## 全てのプロジェクトはパッケージである

`composer.json`をディレクトリに配置した時点で、そのディレクトリはパッケージとなります。
あなたが`require`をプロジェクトに追加する時、あなたは他のパッケージに依存したパッケージを作っています。
プロジェクトとライブラリの唯一の違いは、プロジェクトは名前のないパッケージということです。

<!--
As soon as you have a `composer.json` in a directory, that directory is a
package. When you add a `require` to a project, you are making a package that
depends on other packages. The only difference between your project and
libraries is that your project is a package without a name.
-->

インストール可能なパッケージを作成するために、あなたはそれに名前をつける必要があります。
そのため`composer.json`に`name`を追加してください。

<!--
In order to make that package installable you need to give it a name. You do
this by adding a `name` to `composer.json`:
-->

    {
        "name": "acme/hello-world",
        "require": {
            "monolog/monolog": "1.0.*"
        }
    }

この例では、プロジェクト名は`acme/hello-world`です。`acme`はベンダー名です。ベンダー名は必須です。

<!--
In this case the project name is `acme/hello-world`, where `acme` is the
vendor name. Supplying a vendor name is mandatory.
-->

> **注意:** もしベンダー名に何をつけていいかわからない場合は、あなたのGitHubのユーザネームをつけるといいでしょう。
パッケージ名は大文字小文字を区別しないので、全て小文字で単語の区切りはダッシュで行うのが規約です。

<!--
> **Note:** If you don't know what to use as a vendor name, your GitHub
username is usually a good bet. While package names are case insensitive, the
convention is all lowercase and dashes for word separation.
-->

## プラットフォームパッケージ

Composerはプラットフォームパッケージを持っています。これはシステムにインストールされる仮想的なパッケージで、
実際にはComposerではインストールされません。
これはPHP自身やPHPのエクステンション、システムライブライリを含みます。

<!--
Composer has platform packages, which are virtual packages for things that are
installed on the system but are not actually installable by Composer. This
includes PHP itself, PHP extensions and some system libraries.
-->

* `php`はユーザのPHPバージョンを表します。これには`>=5.4.0`のような制約を適用できます。
    PHPの64bitバージョを要求するため、`php-64bit`のパッケージを指定出来ます。

* `ext-<name>`はPHPのエクステンションを要求します。(コアエクステンション含みます)。
  バージョニングには全く一致しないことがあります。なので制約には`*`を設定するのが概ね良い方法です。
  エクステンションのパッケージ名の例は`ext-gd`です。

* `lib-<name>`はPHPで使われるライブラリのバージョンを制約します。
  次のものが利用できます: `curl`, `iconv`, `libxml`, `openssl`,
  `pcre`, `uuid`, `xsl`.

<!--
* `php` represents the PHP version of the user, allowing you to apply
   constraints, e.g. `>=5.4.0`. To require a 64bit version of php, you can
   require the `php-64bit` package.

* `ext-<name>` allows you to require PHP extensions (includes core
  extensions). Versioning can be quite inconsistent here, so it's often
  a good idea to just set the constraint to `*`.  An example of an extension
  package name is `ext-gd`.

* `lib-<name>` allows constraints to be made on versions of libraries used by
  PHP. The following are available: `curl`, `iconv`, `libxml`, `openssl`,
  `pcre`, `uuid`, `xsl`.
-->

`composer show --platform`を使うと、ローカルで利用できるプラットフォームパッケージのリストを得ることができます。

<!--
You can use `composer show --platform` to get a list of your locally available
platform packages.
-->

## バージョンの指定

いくつかの方法でパッケージのバージョンを指定する必要があります。
パッケージをPackagistで公開するときは、VCS(git, svn, hg)からバージョンを推測することができます。
この場合、バージョンを指定する必要はありませんし、指定しないことが推奨されます。
どのようにバージョンが指定されるかは、[タグ](#tags)と[ブランチ](#branches)を参照してください。

<!--
You need to specify the package's version some way. When you publish your
package on Packagist, it is able to infer the version from the VCS (git, svn,
hg) information, so in that case you do not have to specify it, and it is
recommended not to. See [tags](#tags) and [branches](#branches) to see how
version numbers are extracted from these.
-->

手動でパッケージを作っていて、本当に明示的にバージョンを指定しなければならない場合は、
`version`フィールドを追加します。

<!--
If you are creating packages by hand and really have to specify it explicitly,
you can just add a `version` field:
-->

    {
        "version": "1.0.0"
    }

> **注意:** バージョンフィールドを明示的に指定することは避けるべきです。
> なぜなら、タグの値がタグ名にマッチしなければならないからです。

<!--
> **Note:** You should avoid specifying the version field explicitly, because
> for tags the value must match the tag name.
-->

<h3 id="tags"> タグ </h3>

バージョンのように見えるタグでパッケージのバージョンが作成されます。
これは'X.Y.Z'または'vX.Y.Z'にマッチする形式で、オプションでサフィックス
`-patch`,`-alpha`,`-beta`または`-RC`がつきます。
また、サフィックスには数値をつけることもできます。

<!--
For every tag that looks like a version, a package version of that tag will be
created. It should match 'X.Y.Z' or 'vX.Y.Z', with an optional suffix
of `-patch`, `-alpha`, `-beta` or `-RC`. The suffixes can also be followed by
a number.
-->

以下が、有効なタグ名の例になります:

<!--
Here are a few examples of valid tag names:
-->

    1.0.0
    v1.0.0
    1.10.5-RC1
    v4.4.4beta2
    v2.0.0-alpha
    v2.0.4-p1

<h3 id="branches">ブランチ</h3>

ブランチでパッケージの開発バージョンを作成できます。
ブランチがバージョンのように見える場合、そのバージョンは`{branchname}-dev`となります。
例えば`2.0`ブランチは`2.0.x-dev`バージョンとなります。
(`.x`はそれがブランチであることを認識するために、技術的な理由で追加されます。
`2.0.x`ブランチも有効で同様に`2.0.x-dev`となります。
ブランチがバージョンのように見えない場合、`dev-{branchname}`になります。`master`は`dev-master`となります。

<!--
For every branch, a package development version will be created. If the branch
name looks like a version, the version will be `{branchname}-dev`. For example
a branch `2.0` will get a version `2.0.x-dev` (the `.x` is added for technical
reasons, to make sure it is recognized as a branch, a `2.0.x` branch would also
be valid and be turned into `2.0.x-dev` as well. If the branch does not look
like a version, it will be `dev-{branchname}`. `master` results in a
`dev-master` version.
-->

以下が、ブランチ名の例になります:

<!--
Here are some examples of version branch names:
-->

    1.x
    1.0 (equals 1.0.x)
    1.1.x

> **注意:** 開発バージョンをインストールするとき、自動で`source`からpullされます。
> 詳細は[`install`](03-cli.html#install)コマンドを参照してください。

<!--
> **Note:** When you install a development version, it will be automatically
> pulled from its `source`. See the [`install`](03-cli.md#install) command
> for more details.
-->

<h3 id="aliases">エイリアス</h3>

ブランチ名にバージョンのエイリアスをつけることができます。
例えば、`dev-master`のエイリアスに`1.0.x-dev`つけることができます。
これですべてのパッケージで`1.0.x-dev`を要求することができます。

<!--
It is possible to alias branch names to versions. For example, you could alias
`dev-master` to `1.0.x-dev`, which would allow you to require `1.0.x-dev` in all
the packages.
-->

詳細は[エイリアス](articles/aliases.html)を参照してください。

<!--
See [Aliases](articles/aliases.md) for more information.
-->

<h2 id="lock-file">ロックファイル</h2>

お望みならライブラリに`composer.lock`ファイルをコミットできます。
これはチームが常に同じ依存バージョンでテストする際の助けになります。
しかし、このロックファイルはこれに依存している他のプロジェクトにいかなる影響ももたらしません。
これはメインのプロジェクトのみに影響します。

<!--
For your library you may commit the `composer.lock` file if you want to. This
can help your team to always test against the same dependency versions.
However, this lock file will not have any effect on other projects that depend
on it. It only has an effect on the main project.
-->

もしロックファイルをコミットしたくなくてgitを使っている場合は、それを`.gitignore`に追加してください。

<!--
If you do not want to commit the lock file and you are using git, add it to
the `.gitignore`.
-->

<h2 id="publishing-to-a-vcs">VCSに公開する</h2>

`composer.json`ファイルを含むvcsリポジトリ(バージョンコントロールシステム、例:git)
を持っているのならば、ライブラリはすでにcomposerでインストール可能です。
この例ではGitHubで`acme/hello-world`ライブラリを`github.com/username/hello-world`として公開するとしましょう。

<!--
Once you have a vcs repository (version control system, e.g. git) containing a
`composer.json` file, your library is already composer-installable. In this
example we will publish the `acme/hello-world` library on GitHub under
`github.com/username/hello-world`.
-->

それでは`acme/hello-world`パッケージのインストールをテストするため、
ローカルに新しいプロジェクトを作成しましょう。私たちはそれを`acme/blog`とよぶことにします。
このブログは`acme/hello-world`に依存し、それはさらに`monolog/monolog`に依存しています。
これは、以下の`composer.json`を含む新しい`blog`ディレクトリを作成することで成し遂げられます:

<!--
Now, to test installing the `acme/hello-world` package, we create a new
project locally. We will call it `acme/blog`. This blog will depend on
`acme/hello-world`, which in turn depends on `monolog/monolog`. We can
accomplish this by creating a new `blog` directory somewhere, containing a
`composer.json`:
-->

    {
        "name": "acme/blog",
        "require": {
            "acme/hello-world": "dev-master"
        }
    }

nameはこのケースでは必須ではありません。このブログをライブラリとして公開しないからです。
これは`composer.json`の説明を明確にするために追加しています。

<!--
The name is not needed in this case, since we don't want to publish the blog
as a library. It is added here to clarify which `composer.json` is being
described.
-->

このブログアプリに依存物`hello-world`がどこで見つけられるのか知らせる必要があります。
これはパッケージのリポジトリ指定をこのブログの`composer.json`に追加することでできます:

<!--
Now we need to tell the blog app where to find the `hello-world` dependency.
We do this by adding a package repository specification to the blog's
`composer.json`:
-->

    {
        "name": "acme/blog",
        "repositories": [
            {
                "type": "vcs",
                "url": "https://github.com/username/hello-world"
            }
        ],
        "require": {
            "acme/hello-world": "dev-master"
        }
    }

パッケージリポジトリが動作や、他にどのようなタイプが利用できるかの詳細は、
[リポジトリ](05-repositories.html)を参照してください。

<!--
For more details on how package repositories work and what other types are
available, see [Repositories](05-repositories.md).
-->

これで全てです。
Composerの`install`コマンドを実行することで、
依存関係をインストールすることができます！

<!--
That's all. You can now install the dependencies by running Composer's
`install` command!
-->

**要約:** `composer.json`を含むあらゆるgit/svn/hgリポジトリは
パッケージリポジトリを指定し`require`フィールドで依存物を定義することで、
プロジェクトに追加することができます。

<!--
**Recap:** Any git/svn/hg repository containing a `composer.json` can be added
to your project by specifying the package repository and declaring the
dependency in the `require` field.
-->

<h2 id="publishing-to-packagist">packagistに公開する</h2>

よろしい、今やパッケージを公開できるようになりました。
しかし、毎回vcsリポジトリを指定するのはやっかいなことです。
全てのユーザにそんなことはさせたくないでしょう。

<!--
Alright, so now you can publish packages. But specifying the vcs repository
every time is cumbersome. You don't want to force all your users to do that.
-->

あなたは`monolog/monolog`リポジトリを指定していないことに、気づいているかもしれません。
これはどのような仕組みでしょうか？答えはpackagistです。

<!--
The other thing that you may have noticed is that we did not specify a package
repository for `monolog/monolog`. How did that work? The answer is packagist.
-->

[Packagist](https://packagist.org/)はComposerのメインパッケージリポジトリで、
デフォルトで有効になっています。
packagistで公開されている全てのものは自動的にComposerで利用可能です。
monolog[はpackagistにある](https://packagist.org/packages/monolog/monolog)ので、
私たち追加のリポジトリ指定なくして依存できるのです。

<!--
[Packagist](https://packagist.org/) is the main package repository for
Composer, and it is enabled by default. Anything that is published on
packagist is available automatically through Composer. Since monolog
[is on packagist](https://packagist.org/packages/monolog/monolog), we can depend
on it without having to specify any additional repositories.
-->

私たちが`hello-world`を世界に共有したいと考えた場合、packagistに公開するのがよいでしょう。
本当に簡単にできます。

<!--
If we wanted to share `hello-world` with the world, we would publish it on
packagist as well. Doing so is really easy.
-->

単純に"Submit Package"ボタンをクリックし、サインアップします。
そしてVCSリポジトリのURLを送信してください。
packagistはそのポイントをクローリングをはじめます。
完了すると、パッケージはあらゆる人に利用可能になります。

<!--
You simply hit the big "Submit Package" button and sign up. Then you submit
the URL to your VCS repository, at which point packagist will start crawling
it. Once it is done, your package will be available to anyone.
-->

<p class="prev-next">
  &larr; [基本的な使い方](01-basic-usage.html) |  [コマンドラインインターフェース](03-cli.html) &rarr;
</p>

<!--
&larr; [Basic usage](01-basic-usage.md) |  [Command-line interface](03-cli.md) &rarr;
-->
