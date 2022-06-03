---
description: Trucs per sobreviure en Laravel
---

# Tricks Laravel

## Blade

### Nom usuari autenticat en Blade

```php
{{Auth::user()->name}}
{{Auth::user()->//metodes de model User
```

### Definir una secció autenticat en menú

```php
@auth
     <li class="nav-item">
        <a class="nav-link" href="{{route('users.index')}}">Users</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="{{route('properties.index')}}">Properties</a>
    </li>
@endauth
                    
```

### Select form en formulari update amb opció preseleccionada

```php
<form action="{{route('profile.properties.update',['user'=>Auth::user(),'property'=>$property])}}" method="POST" enctype="multipart/form-data">
    @csrf
    @method('PUT')
    Description
    <br/>
    <textarea name="description"  class="form form-control" >{{$property->description}}</textarea>
    Price
    <br/>
    <input type="text" name="price" value="{{$property->price}}" class="form form-control">
    Type
    <br/>
    <select class="form-control" name="type" value="{{old($property->type)}}">
    @foreach($types as $type)
        <option value="{{ $type }}" @if(old('type',$property->type)==$property->type) 'selected' @endif  >{{$type}}</option>
    @endforeach
    </select>
    <br/>
    Photo:
    <input type="file" name="photo" class="form-control-file">
    <br/>
    <br/>
    @if($property->photo!=null)<img src="{{asset('storage/'.$property->photo)}}" width="150px">@endif
    <br/>
    <br/>
    <input type="submit" class="btn btn-primary" value="Save">
    <br/>
    <br/>
</form>
```

### Blade, eliminar objecte a través de mètode DELETE

```php
<form action="{{route('videos.destroy',$video)}}" method="POST">
 <!--  <input name="_method" type="hidden" value="DELETE"> -->
      @csrf
      @method("DELETE")
      <button class="btn btn-danger">Remove</button>
</form>
```

### Paginació amb bootstrap

Modificar **AppServiceProvider** en Providers:

```php
 public function boot()
    {
        Paginator::useBootstrap();
    }
```

En el moment de treure la col·lecció, apliquem **`paginate(n)`** amb el número d'elements visualitzats per pàgina.

I en blade:

```php
{{-- Pagination --}}
<div class="d-flex justify-content-center">
    {!! $videos->links() !!}
</div>
```

### No carrega css ja que demana esquema https

Si tenim un problema amb la funció  `asset`  a l'hora de carregar recursos amb protocol HTTP en comptes de HTTPS que és el que utilitza el nostre lloc, causant un problema de   "Mixed content" .

Per arreglar-ho cal afegir   `\URL::forceScheme('https')` en el fitxer  `AppServiceProvider,` dins de `Providers,` indicant si estem en producció.

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        if(config('app.env') === 'production') {
            \URL::forceScheme('https');
        }
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }
}
```

\


### Personalitzar les vistes d'errors

Publicar les pàgines d'error internes de laravel a les vistes en resources, per modificar-les al nostre gust:

```php
 php artisan vendor:publish --tag=laravel-errors 
```

![Fitxers copiats i modificables](../.gitbook/assets/captura-de-pantalla-2021-03-08-a-les-19.13.40.png)

### Recòrrer una col·lecció amb dades buides

```php
 @forelse($videos as $video)
            <div class="col-lg-4 bg-dark">
                <div class="card m-2">
                    <div class="card-header">
                        {{$video->title}}
                    </div>
                    <div class="card-body">
                        <div style="overflow: hidden; width: 100%; height: 150px;">
                            <a href="{{route('videos.show',$video)}}">
                            <video autoplay loop muted>
                            <source src="{{asset('storage/'.$video->url)}}" type="video/mp4">
                        </video></a>
                        </div>
                    </div>
                    <div class="card-footer">
                        <cite>Valoració</cite>
                    </div>
                </div>
            </div>
    @empty
                No videos found yet
    @endforelse
```

## Desplegament en carpeta en producció

En aquest cas és necessari procedir a crear en la mateixa carpeta del projecte un fitxer .htaccess que permeti redirigir tot cap a **public/.**

```
// .htaccess en carpeta de projecte
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} -d [OR]
RewriteCond %{REQUEST_FILENAME} -f
RewriteRule ^ ^$1 [N]
RewriteCond %{REQUEST_URI} (\.\w+$) [NC]
RewriteRule ^(.*)$ public/$1
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ server.php
```
