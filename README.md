MultiplyList ActiveRecord Behavior for Yii
==========================

Adds new property into model for getting related list as array of primary keys.

It can be helpful for working with `CHtml::activeCheckBoxList()` and multiple `CHtml::activeDropDownList()`.

[README RUS](http://www.elisdn.ru/blog/26/chekboksi-dlia-sviazei-mnogie-ko-mnogim-v-yii)

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
    public function relations()
    {
        return array(
            'categories'=>array(self::MANY_MANY, 'Category', '{{post_category}}(post_id, category_id)'),
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

    protected function afterSave()
    {
        $this->refreshCategories();
        parent::afterSave();
    }

    protected function refreshCategories()
    {
        $categories = $this->categoriesArray;
 
        PostCategory::model()->deleteAllByAttributes(array('post_id'=>$this->id));
 
        if (is_array($categories))
        {
            foreach ($categories as $id)
            {
                if (Category::model()->exists('id=:id', array(':id'=>$id)))
                {
                    $postCat = new PostCategory();
                    $postCat->post_id = $this->id;
                    $postCat->category_id = $id;
                    $postCat->save();
                }
            }
        }
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
