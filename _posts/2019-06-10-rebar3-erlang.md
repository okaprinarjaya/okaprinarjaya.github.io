---
layout: post
title: Rebar3 Erlang
---

## Contoh penggunaan dasar

### Membuat release

Membuat release / project baru

```
$ rebar3 new release your_app_name
===> Writing your_app_name/apps/your_app_name/src/your_app_name_app.erl
===> Writing your_app_name/apps/your_app_name/src/your_app_name_sup.erl
===> Writing your_app_name/apps/your_app_name/src/your_app_name.app.src
===> Writing your_app_name/rebar.config
===> Writing your_app_name/config/sys.config
===> Writing your_app_name/config/vm.args
===> Writing your_app_name/.gitignore
===> Writing your_app_name/LICENSE
===> Writing your_app_name/README.md

$ cd your_app_name
```

Kita pilih pembuatan HTTP JSON API sebagai contoh hasil project. Library web server yang kita gunakan adalah [Cowboy](https://www.digitalocean.com/docs/networking/floating-ips/quickstart/) , dan library
untuk handle JSON nya kita menggunakan [Jiffy](https://github.com/davisp/jiffy) . 

Buka file `rebar.config` tambahkan library dependencies ke section `deps`. Dan tambahkan plugins `rebar3_run` di section `plugins`

```erl
...
...
...
{deps, [
  {cowboy, {git, "https://github.com/ninenines/cowboy", {tag, "2.6.3"}}},
  {jiffy, {git, "https://github.com/davisp/jiffy", {tag, "0.15.2"}}}
]}.

{plugins, [rebar3_run]}.
...
...
...
```

Buka file `your_app_name/apps/your_app_name/src/your_app_name_app.erl`. Isikan code seperti berikut: 

```erl
%%%-------------------------------------------------------------------
%% @doc your_app_name public API
%% @end
%%%-------------------------------------------------------------------

-module(your_app_name_app).

-behaviour(application).

-export([start/2, stop/1]).

start(_StartType, _StartArgs) ->
  Routes = [
    {'_', [
      {"/", route_root_handler, []}
    ]}
  ],
  Dispatch = cowboy_router:compile(Routes),
  {ok, _} = cowboy:start_clear(
    http,
    [{port, 8080}],
    #{env => #{dispatch => Dispatch}}
  ),
  your_app_name_sup:start_link().

stop(_State) ->
  ok.

%% internal functions

```

Buat file baru bernama `route_root_handler.erl` di `your_app_name/apps/your_app_name/src/`. Isikan code seperti berikut:

```erl
-module(route_root_handler).

-export([init/2]).

init(Req, State) ->
  RespStructure = {[
    {code, <<"ERLANG HANDLER">>},
    {message, <<"ERLANG HANDLER">>},
    {data, {[
      {contents, [
        {[{name, <<"Oka">>}, {email, <<"okaprinarjaya@gmail.com">>}]},
        {[{name, <<"Robert Virding">>}, {email, <<"rvirding@erlang.org">>}]},
        {[{name, <<"Joe Armstrong">>}, {email, <<"joe.armstrong@erlang.org">>}]},
        {[{name, <<"Mike">>}, {email, <<"mike@erlang.org">>}]},
        {[{name, <<"Tony Hoare">>}, {email, <<"tony.hoare@erlang.org">>}]}
      ]},
      {pagingState, <<"12oimfoeimfw85p598utw4igjin">>}
    ]}}
  ]},
  Request = cowboy_req:reply(
    200,
    #{<<"content-type">> => <<"application/json">>},
    jiffy:encode(RespStructure),
    Req
  ),
  {ok, Request, State}.

```

Buka file `your_app_name/apps/your_app_name/src/your_app_name.app.src`. Tambahkan Cowboy dan Jiffy sebagai application 
dependencies di section `applications`.

```
...
...
...
{applications, [
    kernel,
    stdlib,
    cowboy,
    jiffy
  ]},
...
...
...
```

Lanjutkan jalankan perintah-perintah berikut:

```
$ cd your_app_name
$ rebar3 compile
$ rebar3 release
```

Jika ada masalah / error dengan plugin `rebar3_run` saat menjalankan perintah `rebar3 compile`, maka kamu perlu mengubah file: 
`your_app_name/_build/default/plugins/rebar3_run/c_src/Makefile`. Ubah file `Makefile` *HANYA* pada baris: 34 s/d 38. Dengan 
perubahan sebagai berikut: 

```
...
...
...
CFLAGS += -fPIC -I "$(ERTS_INCLUDE_DIR)" -I "$(ERL_INTERFACE_INCLUDE_DIR)"
CXXFLAGS += -fPIC -I "$(ERTS_INCLUDE_DIR)" -I "$(ERL_INTERFACE_INCLUDE_DIR)"

LDLIBS += -L "$(ERL_INTERFACE_LIB_DIR)" -lerl_interface -lei
LDFLAGS += -shared
...
...
...
```

Hasil dari perintah `rebar3 release` adalah sebuah direktori di `your_app_name/_build/default/rel/your_app_name`. 
Nyalakan service HTTP JSON API dengan perintah: `./_build/default/rel/your_app_name/bin/your_app_name start`.
Untuk menghentikan service, jalankan perintah `./_build/default/rel/your_app_name/bin/your_app_name stop`.

Kamu mengcopy direktori `your_app_name/_build/default/rel/your_app_name` ke direktori manapun karena itu adalah sebuah release 
yang sudah lengkap include dengan semua library yang dibutuhkan.

### Start aplikasi di Erlang shell

Jika kamu belum membutuhkan menjalankan aplikasi kamu sebagai aplikasi yang berjalan sebagai OS daemon, maka kamu bisa 
menjalankan perintah `rebar3 run` secara langsung tanpa perlu menjalankan `rebar3 compile`, `rebar3 release`. 

Perintah `rebar3 run` akan membuka Erlang shell yang mana di dalam Erlang shell kamu bisa melakukan segala hal yang terkait 
dengan level development. 

## Perintah dasar
Rebar3 memiliki beberapa perintah dasar untuk penggunaan yang paling dasar yaitu: `new`, `compile`, `release`.

### App atau Release
Di dalam perintah `new` ada opsi `app`, dan `release`. 

App atau Release adalah jenis project. Jenis project App adalah project yg berisikan hanya satu application. 
