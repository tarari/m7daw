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



