---
description: Trucs per sobreviure en Laravel
---

# Tricks Laravel

## Blade

### Nom usuari autenticat en Blade

```php
{{Auth::user()->name}}
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



