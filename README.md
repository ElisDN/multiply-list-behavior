MultiplyList ActiveRecord Behavior for Yii
==========================

Adds new property into model for getting related list as array of primary keys.

It can be helpful for working with `CHtml::activeCheckBoxList()` and multiple `CHtml::activeDropDownList()`.

[README RUS](http://www.elisdn.ru/blog/26)

Installation
------------

Extract to `protected/components`.

Usage example
-------------

You can use property `$model->categoriesArray` for example:

~~~
[php]
class Post extends CActiveRecord
{
    public function rules()
    {
        return array(
            array('categoriesArray', 'safe'),
        );
    }

    public function relations()
    {
        return array(
            'categories'=>array(self::MANY_MANY, 'Category', '{{post_category}}(post_id, category_id)'
             ),
        ),
    );

    public function behaviors()
    {
        return array(
            'DMultiplyListBehavior'=>array(
                 'class'=>'DMultiplyListBehavior',
                 'attribute'=>'categoriesArray',
                 'relation'=>'categories',
                 'relationPk'=>'id',
            ),
        );
    }

    // Process new array there if needle
    protected function afterSave()
    {
        foreach ($this->categoriesArray as $id)
        {
            // ...
        }
        parent::afterSave();
    }
}
~~~

Property `$model->categoriesArray` returns array of related PKs like `Array(5, 12, 18, ...)`.

You can use this property as field in your forms:

~~~
[php]
<?php echo $form->checkBoxList($model, 'categoriesArray', CHtml::listData(Category::model()->findAll(), 'id', 'name')); ?>
~~~

and manually process new values in `Post::afterSave()` method or process in your controller after line `$model->attributes=$_POST['Post']`.