# Token Verify plugin for CakePHP3

[![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENSE)
[![Build Status](https://travis-ci.org/mosaxiv/cakephp-token-verify.svg?branch=master)](https://travis-ci.org/mosaxiv/cakephp-token-verify)
[![Packagist](https://img.shields.io/packagist/v/mosaxiv/cakephp-token-verify.svg)]()

Easily issue tokens that can be used for mail authentication.  
No need for token field in table.  
one-time/url-safe/safety :+1:

## Requirements

- PHP 7.0+
- CakePHP 3.0.0+

## Installation

```
composer require mosaxiv/cakephp-token-verify
```

## Example

### reset password

```php
// app/src/Model/Entity/User.php

use Token\Model\Entity\TokenTrait;

class User extends Entity
{
    use TokenTrait;
}

```

```php
// app/src/Controller/UsersController.php

use Token\Util\Token;

class UsersController extends AppController
{

    public function forgotPassword()
    {
        if ($this->request->is('post')) {
            $email = $this->request->getData('email');
            $user = $this->Users->findByEmail($email)->first();
            if ($user) {
                $token = $user->tokenGenerate();
                // send email
            }
        }
    }

    public function resetPassword($token)
    {
        $user = $this->Users->get(Token::getId($token));
        if (!$user->tokenVerify($token)) {
            throw new \Cake\Network\Exception\NotFoundException();
        }

        if ($this->request->is('post')) {
            $user = $this->Users->patchEntity($user, $this->request->getData());
            if ($this->Users->save($user)) {
                // success
            } else {
                // error
            }
        }
    }
}
```
