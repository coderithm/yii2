# LinkableBehavior

This behavior provides support for ActiveRecords that have a page to display its contents. The page can be an action in a Module or simply in a Controller. It will be easier to get links related to this record without having to write Url Route over and over again.

## Usage

Add the behavior to your ActiveRecord that can he hotlinked:

```php
namespace app\models;

use yii\db\ActiveRecord;
use weblement\yii2\behaviors\LinkableBehavior;

class Post extends ActiveRecord
{
  //...

    public function behaviors()
    {
        return ArrayHelper::merge(parent::behaviors(), [
            [
                'class' => LinkableBehavior::className(),
                'route' => '/posts',
                'defaultAction' => 'view',
                'defaultParams' => function ($record) {
                    return [
                        'id' => $record->id,
                        'slug' => $record->slug
                    ];
                },
            ]
        ]);
    }
}
```

This behavior configuration has a default route to `[/posts/view, 'id' => $record->id, 'slug' => $record->slug]`. In case your action is in a Module, you can set the route to `/module/controller`.

With that code in place, you can now use 2 available methods:
 - `getUrlRoute($action = null, $params = [])`
 - `getHotlink($action = null, $params = [], $options = [])`


### Examples (assuming that you use prettyUrl)

#### getUrlRoute()
```php
use yii\helpers\Url;

// /component/view?id=12345
echo Url::to($component->urlRoute);

// /component/update?id=12345
echo Url::to($component->getUrlRoute('update'));

// http://www.yoursite.com/component/profile?id=12345&ref=facebook
echo Url::to($component->getUrlRoute('profile', ['ref' => 'facebook']), true);
```

#### getHotlink()
```php
use yii\helpers\Url;

// <a href="/component/view?id=12345">[[hotlinkTextAttr]]</a>
echo $component->hotLink;

// <a href="/component/update?id=12345">[[hotlinkTextAttr]]</a>
echo $component->getHotlink('update');

// <a href="/component/profile?id=12345&ref=facebook">[[hotlinkTextAttr]]</a>
echo $component->getHotlink('profile', ['ref' => 'facebook']);
```
If you want to use absolute urls, you should set `LinkableBehavior::$useAbsoluteUrl` to `true`.
If you want to disable hotlinks, you should set `LinkableBehavior::$disableHotlink` to `true`. `<span/>` will be used instead of `<a/>`



### More examples
Assume that you have an ActiveRecord as follows:

```php
public function behaviors()
{
    return ArrayHelper::merge(parent::behaviors(), [
        [
            'class' => LinkableBehavior::className(),
            'route' => '/posts',
            'defaultAction' => 'view',
            'hotlinkTextAttr' => 'title',
            'defaultParams' => function ($record) {
                return [
                    'id' => $record->id,
                    'slug' => $record->slug
                ];
            },
        ]
    ]);
}

// using the behavior
$post = Post::find()->where(['id' => 15, 'slug' => 'this-is-a-post'])->one();

var_dump($post->urlRoute);
// returns ['/posts/view', 'id' => 15, 'slug' => 'this-is-a-post']

var_dump($post->getUrlRoute('comments', ['order' => SORT_ASC]));
// returns ['/posts/comments', 'id' => 15, 'slug' => 'this-is-a-post', 'order' => 4]
// SORT_ASC value is 4


echo $post->hotlink;
// returns <a href="/posts/15?slug=this-is-a-post">This is a post</a>

echo $post->getHotlink('comments', ['order' => SORT_ASC], ['class' => 'btn btn-primary']);
// returns <a href="/posts/15/comments?slug=this-is-a-post&order=4">This is a post</a>

```
