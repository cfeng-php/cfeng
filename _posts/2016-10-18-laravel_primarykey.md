---
layout: post
title: Laravel中定义复合主键
tags: [laravel,组合主键]
---

laravel默认主键是id，但有的时候我们建表时可能会需要用到复合主键，那么laravel中使用Eloquent Medel如何定义复合主键呢？直接上代码。

首先在app目录先创建文件 Traits/HasCompositePrimaryKey 内容如下：

```php
<?php
// Adjust this to match your model namespace!
namespace App\Traits;

use Illuminate\Database\Eloquent\Builder;

trait HasCompositePrimaryKey
{
    /**
     * Get the value indicating whether the IDs are incrementing.
     *
     * @return bool
     */
    public function getIncrementing()
    {
        return false;
    }

    /**
     * Set the keys for a save update query.
     *
     * @param  \Illuminate\Database\Eloquent\Builder $query
     * @return \Illuminate\Database\Eloquent\Builder
     */
    protected function setKeysForSaveQuery(Builder $query)
    {
        foreach ($this->getKeyName() as $key) {
            if ($this->$key)
                $query->where($key, '=', $this->$key);
            else
                throw new Exception(__METHOD__ . 'Missing part of the primary key: ' . $key);
        }

        return $query;
    }
}
```

在model中使用：

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Goods extends Model
{
    use \App\Traits\HasCompositePrimaryKey;
    use SoftDeletes;

    protected $primaryKey = ['param1', 'param2']; //设置组合主键

    // coding

}
```


