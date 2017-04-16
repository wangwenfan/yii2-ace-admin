Yii2 ace Admin 后台模板
=======================

### 简介
系统基于yii2高级版本开发，后台模板使用的ace admin。对于一般的后台开发，比较方便; 对于数据表的CURL操作都有封装，且所有操作都有有权限控制。
####特点
* 基于RBAC权限管理
* 视图使用JS控制，数据显示使用的DataTables
* 基于数据表的增、删、改、查都有封装，添加新的数据表操作方便
### 安装要求
* PHP >= 5.4
* MySQL
### 安装
1. 克隆项目
```angular2html
git clone https://github.com/myloveGy/yii2-ace-admin.git
```
2. 执行 composer 安装Yii2
```angular2html
composer install
```
3. 浏览器进入该目录的下执行index.php 其实就是导入数据
### 使用说明
1. 后台控制器配置
```php
namespace backend\controllers;

use common\models\China;

class ChinaController extends Controller 
{
    /**
     * where() 处理查询信息(主要查询、数据导出时候使用)
     * @param array $params 查询时候请求的参数信息（一个数组）
     * @return array 需要返回一个数组
     */
    public function where($params)
    {
        /**
         * 数组配置说明
         * 键对应查询字段
         * 值对应查询配置处理
         * 字符串 'pid' => '=' 处理为 model 查询数组 ['=', 'pid', '查询数值']
         * 数组 'id' => [
         *           'and' => '=',       // 查询类型(默认=)， 其他（>=, 'like', '<='， ...）
         *           'func' => 'intval'  // 对查询数值的处理函数，一般如果是时间查询转时间戳比较好用
         *           // 'field' => 'cid', // 改变查询的字段
         *      ]
         * 匿名函数 'name' => function($key, $value) {
         *   return ['like', 'name', trim($value)];
         * }
         * @param string $key 查询的字段
         * @param string $value 查询的值
         * @return array 需要返回一个数组
         */
        return [
            'id' => ['and' => '=', 'func' => 'intval'],
            'name' => function($key, $value) {
                return ['like', 'name', trim($value)];
            },
            'pid'  => '='
        ];
        
        // 该段配置最终会处理为model 查询的where 条件数组(只有在查询值有效，不为空的情况下，对应字段的查询才会加上)
        // $model->find()->where(['and', ['=', 'id', '查询值'], ['like', 'name', '查询值'], ['=', 'pid', '查询值']])
    }
    
    /**
     * getModel() 获取model
     * @return China
     * @desc 数据的添加、修改、删除、查询都基于该方法返回的model
     */
    public function getModel()
    {
        return new China();
    }
}
```
2. 视图文件JS配置
```js
    var arrParent = {"0": "中国", "1": "湖南"};
    /**
     * 简单配置说明
     * title 配置表格名称
     * table DataTables 的配置 
     * --- aoColumns 中的 value, search, edit 是 meTables 的配置
     * ------ value 为编辑表单redis、select, checkbox， 搜索的表单的select 提供数据源
     * ------ search 搜索表单配置(不配置不会生成查询表单), type 类型支持 text, select 其他可以自行扩展
     * ------ edit 编辑表单配置（不配置不会生成编辑表单）, type 类型支持text, password, file, radio, select, checkbox, textarea 等等 meTable.inputCreate 等后缀命名函数， 可以自行扩展
     * 其他配置查看 meTables 配置
     */
    var m = meTables({
        title: "地址信息",
        table: {
            "aoColumns":[
                {"title": "id", "data": "id", "sName": "id",  "defaultOrder": "desc",
                    "edit": {"type": "text", "required":true,"number":true}
                },
                {"title": "地址名称", "data": "name", "sName": "name",
                    "edit": {"type": "text", "required": true, "rangelength":"[2, 40]"},
                    "search": {"type": "text"},
                    "bSortable": false
                },
                {"title": "父类ID", "data": "pid", "sName": "pid", "value": arrParent,
                    "edit": {"type": "text", "required": true, "number": true},
                    "search": {"type":"select"}
                }
            ]
        }
    });

    $(function(){
        m.init();
    })
```
目录结构
-------------------

```
common
    config/              contains shared configurations
    mail/                contains view files for e-mails
    models/              contains model classes used in both backend and frontend
console
    config/              contains console configurations
    controllers/         contains console controllers (commands)
    migrations/          contains database migrations
    models/              contains console-specific model classes
    runtime/             contains files generated during runtime
backend
    assets/              contains application assets such as JavaScript and CSS
    config/              contains backend configurations
    controllers/         contains Web controller classes
    models/              contains backend-specific model classes
    runtime/             contains files generated during runtime
    views/               contains view files for the Web application
    web/                 contains the entry script and Web resources
frontend
    assets/              contains application assets such as JavaScript and CSS
    config/              contains frontend configurations
    controllers/         contains Web controller classes
    models/              contains frontend-specific model classes
    runtime/             contains files generated during runtime
    views/               contains view files for the Web application
    web/                 contains the entry script and Web resources
    widgets/             contains frontend widgets
vendor/                  contains dependent 3rd-party packages
environments/            contains environment-based overrides
tests                    contains various tests for the advanced application
    codeception/         contains tests developed with Codeception PHP Testing Framework
```
