# Reliese Laravel Model Generator
[![Build Status](https://travis-ci.org/reliese/laravel.svg?branch=master)](https://travis-ci.org/reliese/laravel)
[![Latest Stable Version](https://poser.pugx.org/reliese/laravel/v/stable)](https://packagist.org/packages/reliese/laravel)
[![Total Downloads](https://poser.pugx.org/reliese/laravel/downloads)](https://packagist.org/packages/reliese/laravel)
[![Latest Unstable Version](https://poser.pugx.org/reliese/laravel/v/unstable)](https://packagist.org/packages/reliese/laravel)
[![License](https://poser.pugx.org/reliese/laravel/license)](https://packagist.org/packages/reliese/laravel)

Reliese Laravel Model Generator aims to speed up the development process of Laravel applications by 
providing some convenient code-generation capabilities. 
The tool inspects your database structure, including column names and foreign keys, in order 
to automatically generate Models that have correctly typed properties, along with any relationships to other Models.

## How does it work?

This package expects that you are using Laravel 5.1 or above.
You will need to import the `reliese/laravel` package via composer:

### Configuration

It is recommended that this package should only be used on a local environment for security reasons. You should install it via composer using the --dev option like this:

```shell
composer require reliese/laravel --dev
```

Add the `models.php` configuration file to your `config` directory and clear the config cache:

```shell
php artisan vendor:publish --tag=reliese-models

# Let's refresh our config cache just in case
php artisan config:clear
```

## Models

![Generating models with artisan](https://cdn-images-1.medium.com/max/800/1*hOa2QxORE2zyO_-ZqJ40sA.png "Making artisan code my Eloquent models")

### Usage

Assuming you have already configured your database, you are now all set to go.

- Let's scaffold some of your models from your default connection.

```shell
php artisan code:models
```

- You can scaffold a specific table like this:

```shell
php artisan code:models --table=users
```

- You can also specify the connection:

```shell
php artisan code:models --connection=mysql
```

- If you are using a MySQL database, you can specify which schema you want to scaffold:

```shell
php artisan code:models --schema=shop
```

### Customizing Model Scaffolding

To change the scaffolding behaviour you can make `config/models.php` configuration file
fit your database needs. [Check it out](https://github.com/reliese/laravel/blob/master/config/models.php) ;-)

### Tips

#### 1. Keeping model changes

You may want to generate your models as often as you change your database. In order
not to lose you own model changes, you should set `base_files` to `true` in your `config/models.php`.

When you enable this feature your models will inherit their base configurations from
base models. You should avoid adding code to your base models, since you
will lose all changes when they are generated again.

> Note: You will end up with two models for the same table and you may think it is a horrible idea 
to have two classes for the same thing. However, it is up to you
to decide whether this approach gives value to your project :-)

#### Support

For the time being, this package supports MySQL, PostgreSQL and SQLite databases. Support for other databases are encouraged to be added through pull requests.

## 拉取分支描述
拉取该分支是为了在 phpdoc 中添加字段的描述,没有经过测试仅供自己使用并记录，以便下次修改。   
修改的地方：
+ 配置文件添加 hints 参数（该参数在配置文件中没有，但是阅读源码发现已在使用，设置为 true 会在模型中添加 hints 属性，用来记录字段与描述的对应关系，我将使用这个字段来判断是否添加描述）
+ src\Coders\Model\Factory.php 文件 properties 方法中修改以下内容
```php
        foreach ($model->getProperties() as $name => $hint) {
            $annotations .= $this->class->annotation('property', "$hint \$$name");
        }
// 上方代码修改为下方的代码
        foreach ($model->getProperties() as $name => $hint) {
            if ($model->usesHints()) {
                $comment = @$model->getHints()[$name];
                $annotations .= $this->class->annotation('property', "$hint \$$name {$comment}");
            } else {
                $annotations .= $this->class->annotation('property', "$hint \$$name");
            }
        }
```
