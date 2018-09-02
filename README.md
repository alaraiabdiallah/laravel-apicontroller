# Laravel Default Api Controller

Give you default controller for api. You just need to focus on data and validation

## Example

On CategoryController.php
```php
<?php

namespace App\Http\Controllers;

use App\Category;
use Illuminate\Http\Request;
use App\Http\Resources\CategoryResource;
use Api\Controllers\ApiController;

class CategoryController extends ApiController
{

    protected $rules = [
        'name' => "required|max:20",
        'slug' => "required|alpha_dash|max:20",
    ];

    protected $model,$resource;

    public function __construct()
    {
        $this->middleware('jwtauth', ['except' => ['index', 'show']]);
        $this->model = new Category;
        $this->resource = CategoryResource::class;
    }


    protected function params($request)
    {
        $rules = $this->getRules($request);
        $this->validate($request, $rules);
        return $request->all();
    }

    protected function getRules($request)
    {
        if ($request->isMethod('patch')) {
            $this->rules = [
                'name' => "max:20",
                'slug' => "alpha_dash|max:20",
            ];
        }
        return $this->rules;
    }

}

```

On route file
```php
<?php

$router->group(['prefix' => 'categories'], function () use ($router) {
    $controller = "CategoryController";
    $router->get('/', "$controller@index");
    $router->post('/', "$controller@store");
    $router->get('/{id}', "$controller@show");
    $router->patch('/{id}', "$controller@update");
    $router->delete('/{id}', "$controller@destroy");
});
```