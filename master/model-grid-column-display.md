# 列的显示和扩展


`model-grid`内置了很多对于列的操作方法，可以通过这些方法很灵活的操作列数据。

## 列显示

`model-grid`内置了若干方法来帮助你扩展列功能

### display

`Dcat\Admin\Grid\Column`对象内置了`display()`方法来通过传入的回调函数来处理当前列的值，
```php
$grid->column('title')->display(function ($title) {

    return "<span style='color:blue'>$title</span>";
    
});
```
在传入的匿名函数中可以通过任何方式对数据进行处理，另外匿名函数绑定了当前列的数据作为父对象，可以在函数中调用当前行的数据：
```php
$grid->first_name();

$grid->last_name();

// 不存在的`full_name`字段
$grid->column('full_name')->display(function () {
    return $this->first_name . ' ' . $this->last_name;
});
```

### 设置列的HTML属性
设列的`html`属性
```php
$grid->name->setAttributes(['style' => 'font-size:14px']);
```

### 列视图
`view`方法可以引入一个视图文件。
```php
$grid->content->view('admin.fields.content');
```

默认会传入视图的三个变量：
 - `$model` 当前行数据
 - `$name` 字段名称
 - `$value` 为当前列的值
 
模板代码如下：
```blade
<label>名称：{{ $name }}</label>
<label>值：{{ $value }}</label>
<label>其他字段：{{ $model->title }}</label>
```



### 开关


快速将列变成开关组件，使用方法如下：
```php
$grid->status()->switch();
```
这个功能需要你在`form`表单方法中同样设置一个`status`字段

```php
$form->hidden('status')->saving(function ($v) {
    return $v ? '打开' : '关闭';
});

// 或者
$form->switch('status')->saving(function ($v) {
    return $v ? '打开' : '关闭';
});
```


### 开关组

> {tip} 注意：在`grid`中对某字段设置`switchGroup`默认的保存结果是`0`或`1`，如需修改可以通过`$form->hidden(xxx)->saving(...)`方法修改。

快速将列变成开关组件组，使用方法如下：
```php
$grid->switch_group->switchGroup([
    'hot'        => '热门',
    'new'        => '最新',
    'recommend'  => '推荐',
    'image.show' => '显示图片', // 更新对应关联模型
]);
// 或
// 不写label会自动从翻译文件翻译，具体使用请参照 “字段翻译” 章节
$grid->switch_group->switchGroup(['is_new', 'is_hot', 'published']);
```

这个功能需要你在`form`表单方法中同样设置对应的字段

```php
$form->switch('hot')->saving(function ($v) {
    return $v ? '打开' : '关闭';
});

$form->switch('new')->saving(function ($v) {
    return $v ? '打开' : '关闭';
});
```


![]({{public}}/assets/img/screenshots/grid-column-switch-group.png)


### 下拉选框

```php
$grid->options()->select([
    1 => 'Sed ut perspiciatis unde omni',
    2 => 'voluptatem accusantium doloremque',
    3 => 'dicta sunt explicabo',
    4 => 'laudantium, totam rem aperiam',
]);
```

`select` 也支持参数为闭包，使用方法和`editable`的`select`类似。

![]({{public}}/assets/img/screenshots/grid-column-select.png)

### 单选框
```php
$grid->options()->radio([
    1 => 'Sed ut perspiciatis unde omni',
    2 => 'voluptatem accusantium doloremque',
    3 => 'dicta sunt explicabo',
    4 => 'laudantium, totam rem aperiam',
]);
```

`radio` 也支持参数为闭包，使用方法和`editable`的`select`类似。

### 多选框
```php
$grid->options()->checkbox([
    1 => 'Sed ut perspiciatis unde omni',
    2 => 'voluptatem accusantium doloremque',
    3 => 'dicta sunt explicabo',
    4 => 'laudantium, totam rem aperiam',
]);
```

`checkbox` 也支持参数为闭包，使用方法和`editable`的`select`类似。
![]({{public}}/assets/img/screenshots/grid-column-checkbox.png)


### 图片

```php
$grid->picture()->image();

//设置服务器和宽高
$grid->picture()->image('http://xxx.com', 100, 100);

// 显示多图
$grid->pictures()->display(function ($pictures) {
    
    return json_decode($pictures, true);
    
})->image('http://xxx.com', 100, 100);
```

### 显示label标签

支持`Dcat\Admin\Color`类中内置的所有颜色

```php
use Dcat\Admin\Admin;

$grid->name()->label();

// 设置颜色，直接传别名
$grid->name()->label('danger');

// 也可以这样使用
$grid->name()->label(Admin::color()->danger());

// 也可以直接传颜色代码
$grid->name()->label('#222');
```

给不同的值设置不同的颜色
```php
use Dcat\Admin\Admin;

$grid->state->using([1 => '未处理', 2 => '已处理', ...])->label([
    'default' => 'primary', // 设置默认颜色，不设置则默认为 default
    
	1 => 'primary',
	2 => 'danger',
	3 => 'success',
	4 => Admin::color()->info(),
]);
```

### 显示badge标签

支持`Dcat\Admin\Color`类中内置的所有颜色

```php
$grid->name()->badge();

// 设置颜色，直接传别名
$grid->name()->badge('danger');

// 也可以这样使用
$grid->name()->badge(Admin::color()->danger());

// 也可以直接传颜色代码
$grid->name()->badge('#222');
```

给不同的值设置不同的颜色
```php
use Dcat\Admin\Admin;

$grid->state->using([1 => '未处理', 2 => '已处理', ...])->badge([
    'default' => 'primary', // 设置默认颜色，不设置则默认为 default	
    
    1 => 'primary',
	2 => 'danger',
	3 => 'success',
	4 => Admin::color()->info(),
]);
```

### 圆点前缀 (dot)

通过`dot`方法可以在列文字前面加上一个带颜色的圆点

> {tip} `Since v1.2.5` 支持`Dcat\Admin\Color`类中内置的所有颜色

```php
use Dcat\Admin\Admin;

$grid->state
	->using([1 => '未处理', 2 => '已处理', ...])
	->dot(
		[
			1 => 'primary',
			2 => 'danger',
			3 => 'success',
			4 => Admin::color()->info(),
		], 
	    'primary' // 第二个参数为默认值
	);
```

效果

<a href="{{public}}/assets/img/screenshots/grid-column-dot.png" target="_blank">
    <img style="box-shadow:0 1px 6px 1px rgba(0, 0, 0, 0.12)" width="150px" src="{{public}}/assets/img/screenshots/grid-column-dot.png">
</a>


### 列展开
`expand`方法可以把内容隐藏，点击按钮的时候显示在表格下一行
```php
$grid->content
    ->display('详情') // 设置按钮名称
    ->expand(function () {
        // 返回显示的详情
        // 这里返回 content 字段内容，并用 Card 包裹起来
        $card = new Card(null, $this->content);
    
        return "<div style='padding:10px 10px 0'>$card</div>";
    });
```
也可以通过以下方式设置按钮
```php
$grid->content->expand(function (Grid\Displayers\Expand $expand) {
    // 设置按钮名称
    $expand->button('详情');

    // 返回显示的详情
    // 这里返回 content 字段内容，并用 Card 包裹起来
    $card = new Card(null, $this->content);

    return "<div style='padding:10px 10px 0'>$card</div>";
});
```


### 弹出模态框
`modal`方法可以把内容隐藏，点击按钮的时候显示在表格下一行
```php
$grid->content
    ->display('查看') // 设置按钮名称
    ->modal(function () {
        $card = new Card(null, $this->content);
    
        return "<div style='padding:10px 10px 0'>$card</div>";
    });
```

### 进度条
`progressBar`进度条
```php
$grid->rate->progressBar();

//设置颜色，默认`primary`,可选`danger`、`warning`、`info`、`primary`、`success`
$grid->rate->progressBar('success');

// 设置进度条尺寸和最大值
$grid->rate->progressBar('success', 'sm', 100);
```

### showTreeInDialog
`showTreeInDialog`方法可以把一个带有层级关系的数组呈现为树形弹窗，比如权限就可以用此方法展示
```php
// 查出所有的权限数据
$nodes = (new $permissionModel)->allNodes();

// 传入二维数组（未分层级）
$grid->permissions->showTreeInDialog($nodes);

// 也可以传入闭包
$grid->permissions->showTreeInDialog(function (Grid\Displayers\DialogTree $tree) use (&$nodes, $roleModel) {
    // 设置所有节点
    $tree->nodes($nodes);
    
    // 设置节点数据字段名称，默认"id"，"name"，"parent_id"
    $tree->setIdColumn('id');
    $tree->setTitleColumn('title');
    $tree->setParentColumn('parent_id');

    // $this->roles 可以获取当前行的字段值
    foreach (array_column($this->roles, 'slug') as $slug) {
        if ($roleModel::isAdministrator($slug)) {
            // 选中所有节点
            $tree->checkedAll();
        }
    }
});
```
<a href="{{public}}/assets/img/screenshots/grid-column-tree.png" target="_blank">
    <img style="box-shadow:0 1px 6px 1px rgba(0, 0, 0, 0.12)" width="100%" src="{{public}}/assets/img/screenshots/grid-column-tree.png">
</a>

### 内容映射using
```php
$grid->status->using([0 => '未激活', 1 => '正常']);

// 第二个参数为默认值
$grid->gender->using([1 => '男', 2 => '女'], '未知');
```

### 字符串分割为数组
`explode`方法可以把字符串分割为数组。
```php
$grid->tag->explode()->label();

// 可以指定分隔符，默认","
$grid->tag->explode('|')->label();
```

### prepend

`prepend` 方法用于给 `string` 或 `array` 类型的值前面插入内容。

```php
// 当字段值是一个字符串
$grid->email->prepend('mailto:');

// 当字段值是一个数组
$grid->arr->prepend('first item');
```

从`v1.2.5`版本开始，`prepend`方法允许传入闭包参数
```php
$grid->email->prepend(function ($value, $original) {
    // $value 是当前字段值
    // $original 是当前字段从数据库中查询出来的原始值
    
    // 获取其他字段值
    $username = $this->username;
    
    return "[{$username}]";
});
```

### append
`prepend` 方法用于给 `string` 或 `array` 类型的值后面插入内容。

```php
// 当字段值是一个字符串
$grid->email->append('@gmail.com');

// 当字段值是一个数组
$grid->arr->append('last item');
```

从`v1.2.5`版本开始，`append`方法允许传入闭包参数
```php
$grid->email->prepend(function ($value, $original) {
    // $value 是当前字段值
    // $original 是当前字段从数据库中查询出来的原始值
    
    // 获取其他字段值
    $username = $this->username;
    
    return "[{$username}]";
});
```

### 列字符串或数组截取

```php
// 最多显示50个字符
$grid->contents->limit(50, '...');

// 如果字段值是数组也支持
$grid->tags->limit(3);
```

### 列二维码
```php
$grid->website->qrcode(function () {
    return $this->url;
}, 200, 200);
```

### 可复制
```php
$grid->website->copyable();
```


### 可排序

通过`Column::orderable`可以开启字段可排序功能，此功能需要在你的模型类中`use ModelTree`或`use SortableTrait`，并且需要继承`Spatie\EloquentSortable\Sortable`接口。


#### SortableTrait

如果你的数据不是层级结构数据，可以直接使用`use SortableTrait`，更多用法可参考[eloquent-sortable](https://github.com/spatie/eloquent-sortable)。

模型
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Spatie\EloquentSortable\Sortable;
use Spatie\EloquentSortable\SortableTrait;

class MyModel extends Model implements Sortable
{
    use SortableTrait;

    protected $sortable = [
        // 设置排序字段名称
        'order_column_name' => 'order',
        // 是否在创建时自动排序，此参数建议设置为true
        'sort_when_creating' => true,
    ];
}
```

使用
```php
$grid->model()->orderBy('order');

$grid->order->orderable();
```


#### ModelTree

如果你的数据是层级结构数据，可以直接使用`use Model`，下面以权限模型为例来演示用法

> {tip} `ModelTree`实际上也是继承了[eloquent-sortable](https://github.com/spatie/eloquent-sortable)的功能。

```php
<?php

namespace Dcat\Admin\Models;

use Dcat\Admin\Traits\HasDateTimeFormatter;
use Dcat\Admin\Traits\ModelTree;
use Spatie\EloquentSortable\Sortable;

class Permission extends Model implements Sortable
{
    use HasDateTimeFormatter,
        ModelTree {
            ModelTree::boot as treeBoot;
        }
        
    ...    
}        
```

使用
```php
$grid->order->orderable();
```

效果
<a href="{{public}}/assets/img/screenshots/grid-display-orderable.png" target="_blank">
    <img  src="{{public}}/assets/img/screenshots/grid-display-orderable.png" style="box-shadow:0 1px 6px 1px rgba(0, 0, 0, 0.12)" width="100%">
</a>

## 帮助方法

### 字符串操作
如果当前里的输出数据为字符串，那么可以通过链式方法调用`Illuminate\Support\Str`的方法。

比如有如下一列，显示`title`字段的字符串值:

```php
$grid->title();
```

在`title`列输出的字符串基础上调用`Str::title()`方法
```php
$grid->title()->title();
```
调用方法之后输出的还是字符串，所以可以继续调用`Illuminate\Support\Str`的方法：
```php
$grid->title()->title()->ucfirst();

$grid->title()->title()->ucfirst()->substr(1, 10);

```

### 数组操作
如果当前列输出的是数组，可以直接链式调用`Illuminate\Support\Collection`方法。

比如`tags`列是从一对多关系取出来的数组数据：
```php
$grid->tags();

array (
  0 => 
  array (
    'id' => '16',
    'name' => 'php',
    'created_at' => '2016-11-13 14:03:03',
    'updated_at' => '2016-12-25 04:29:35',
    
  ),
  1 => 
  array (
    'id' => '17',
    'name' => 'python',
    'created_at' => '2016-11-13 14:03:09',
    'updated_at' => '2016-12-25 04:30:27',
  ),
)

```

调用`Collection::pluck()`方法取出数组的中的`name`列
```php
$grid->tags()->pluck('name');

array (
    0 => 'php',
    1 => 'python',
  ),

```
取出`name`列之后输出的还是数组，还能继续调用用`Illuminate\Support\Collection`的方法

```php
$grid->tags()->pluck('name')->map('ucwords');

array (
    0 => 'Php',
    1 => 'Python',
  ),
```
将数组输出为字符串
```php
$grid->tags()->pluck('name')->map('ucwords')->implode('-');

"Php-Python"
```

### 混合使用

在上面两种类型的方法调用中，只要上一步输出的是确定类型的值，便可以调用类型对应的方法，所以可以很灵活的混合使用。

比如`images`字段是存储多图片地址数组的JSON格式字符串类型：
```php

$grid->images();

// "['foo.jpg', 'bar.png']"

// 链式方法调用来显示多图
$grid->images()->display(function ($images) {
    return json_decode($images, true);
    
})->map(function ($path) {
    return 'http://localhost/images/'. $path;
    
})->image();

```


## 扩展列的显示功能

可以通过两种方式扩展列功能，第一种是通过匿名函数的方式。
>扩展列功能方法后IDE默认是不会自动补全的，这时候可以通过`php artisan admin:ide-helper`生成IDE提示文件。

### 匿名函数
在`app/Admin/bootstrap.php`加入以下代码:
```php
use Dcat\Admin\Grid\Column;

// 第二个参数为 `Column` 对象， 第三个参数是自定义参数
Column::extend('color', function ($value, $column, $color) {
    return "<span style='color: $color'>$value</span>"
});
```
然后在`model-grid`中使用这个扩展：
```php
$grid->title()->color('#ccc');
```

### 扩展类
如果列显示逻辑比较复杂，可以通过扩展类来实现。

扩展类`app/Admin/Extensions/Popover.php`:
```php
<?php

namespace App\Admin\Extensions;

use Dcat\Admin\Admin;
use Dcat\Admin\Grid\Displayers\AbstractDisplayer;

class Popover extends AbstractDisplayer
{
    public function display($placement = 'left')
    {
        Admin::script("$('[data-toggle=\"popover\"]').popover()");

        return <<<EOT
<button type="button"
    class="btn btn-secondary"
    title="popover"
    data-container="body"
    data-toggle="popover"
    data-placement="$placement"
    data-content="{$this->value}"
    >
  弹出提示
</button>
EOT;

    }
}
```
然后在`app/Admin/bootstrap.php`注册扩展类：
```php
use Dcat\Admin\Grid\Column;
use App\Admin\Extensions\Popover;

Column::extend('popover', Popover::class);
```
然后就能在`model-grid`中使用了：
```php
$grid->desciption()->popover('right');
```

### 指定列名
出了上述两种方式扩展列功能，我们还可以通过指定列名称的方式扩展列功能

在`app/Admin/bootstrap.php`加入以下代码:
```php
use Dcat\Admin\Grid\Column;

// 这个扩展方法等同于
// $grid->title()->display(function ($value) {
//    return "<span style='color:red'>$value</span>"
// });
Column::define('title', function ($value) {
    return "<span style='color:red'>$value</span>"
});

// 这个扩展方法等同于
// $grid->status()->switch();
Column::define('status', Dcat\Admin\Grid\Displayers\SwitchDisplay::class);
```
然后在`model-grid`中`title`和`status`字段会自动使用以上扩展：
```php
$grid->title();
$grid->status();
```

## 根据条件判断是否使用列的显示功能
有些情况我们需要根据某个条件去判断是否使用列的某个显示功能：
> {tip} 需要注意的是，`Grid\Column::if`只对列的显示相关功能有效，其他方法如表头的相关操作都不能使用此方法！

```php
$grid->config()
    ->if(function () {
        return $this->config ? true : false;
    })
    ->display($view)
    ->expand($this->getExpandHandler('config'))
    ->else()
    ->emptyString();
```
上面写法等同于
```php
$grid->config()
    ->if(function () {
        return $this->config ? true : false;
    })
    ->then(function (Grid\Column $column) {
        $column->display($view)->expand($this->getExpandHandler('config'));
    })
    ->else(function (Grid\Column $column) {
        $column->emptyString();
    });
```

支持多个`if`
```php
$grid->title
    ->if(...)
    ->then(...)
    ->else(...)
    
    ->if(...)
    ->then(...)
    ->else(...);
```


