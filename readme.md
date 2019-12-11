1 ターミナル　laravelいれる

composer create-project laravel/laravel cms 5.5.* --prefer-dist


２　ターミナル　React入れる
php artisan preset react
npm install
npm run dev

３　MAMPの参照先を変える
Mamp > Preferences > Web-Server > DocumentRoot（フォルダアイコン） をクリック
htdocs > laravel > ？？？ > public を選択（サーバー再起動）

４　.envファイルの自分のサーバーとかを変える

<p>5 /app/Providers/ AppServiceProvider.phpを変える
  
  use Illuminate\Support\Facades\Schema; //この行を追加
  Schema::defaultStringLength(191);   //この行を追加
  
６　マイグレーションファイルを作る・編集
　 php artisan make:migration create_books_table --create=books
  
  〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜
  
   public function up()
    {
        Schema::create('books', function (Blueprint $table) {
            $table->increments('id');
            $table->string('item_name');
            $table->integer('item_number');
            $table->integer('item_amount');
            $table->datetime('published');
            $table->timestamps();
        });
    }
    〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜
    
    マイグレーションを実行
    php artisan migrate
 
 ７　モデルの作成
 　 php artisan make:model Book
   
 8　routes/web.php
 
<?php

use App\Book;
use Illuminate\Http\Request;

/**
* 本のダッシュボード表示(books.blade.php)
*/
Route::get('/', function () {
    return view('books');
});

/**
* 新「本」を追加
*/
Route::post('/books', function (Request $request) {
    //
});

/**
* 本を削除
*/
Route::delete('/book/{book}', function (Book $book) {
    //
});

9　views/layouts/app.blade.php

<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html lang="ja">
<head>
<title>Book List</title>
<!-- CSS と JavaScript -->

</head>
<body>
    <div class="container">
        <nav class="navbar navbar-default">
        <!-- ナビバーの内容 -->
        </nav>
    </div>
    @yield('content')
</body>
</html>


10 views/common/errors.blade.php

@if (count($errors) > 0)
    <!-- Form Error List -->
    <div class="alert alert-danger">
        <div><strong>入力した文字を修正してください。</strong></div> 
        <div>
            <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
            </ul>
        </div>
    </div>
@endif


11 routes/web.php

//保存

Route::post('/books', function (Request $request) {
    //バリデーション
    $validator = Validator::make($request->all(), [
        'item_name' => 'required|max:255',
    ]);
    //バリデーション:エラー 
    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }
    //以下に登録処理を記述（Eloquentモデル）
    // Eloquent モデル
    $books = new Book;
    $books->item_name = $request->item_name;
    $books->item_number = '1';
    $books->item_amount = '1000';
    $books->published = '2017-03-07 00:00:00';
    $books->save(); 
    return redirect('/');
});

//取得

Route::get('/', function () {
    $books = Book::orderBy('created_at', 'asc')->get();
    return view('books', [
        'books' => $books
    ]);
    //return view('books',compact('books')); //も同じ意味
});

12 views/books.blade.php

  <!-- 現在の本、formの下らへん -->
    @if (count($books) > 0)
        <div class="card-body">
            <div class="card-body">
                <table class="table table-striped task-table">
                    <!-- テーブルヘッダ -->
                    <thead>
                        <th>本一覧</th>
                        <th>&nbsp;</th>
                    </thead>
                    <!-- テーブル本体 -->
                    <tbody>
                        @foreach ($books as $book)
                            <tr>
                                <!-- 本タイトル -->
                                <td class="table-text">
                                    <div>{{ $book->item_name }}</div>
                                </td>
  
			                           <!-- 本: 削除ボタン -->
                                <td>
                                   <form action="{{ url('book/'.$book->id) }}" method="POST">
                                     {{ csrf_field() }}
                                     {{ method_field('DELETE') }}
                                     <button type="submit" class="btn btn-danger">
                                        削除
                                     </button>
                                   </form>
                                </td>
                            </tr>
                        @endforeach
                    </tbody>
                </table>
            </div>
        </div>
    @endif
    
13 routes/web.php

Route::delete('/book/{book}', function (Book $book) {
    $book->delete();       //追加
    return redirect('/');  //追加
});
  </p>

１４　views/layouts/app.blade.php
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

15 views/books.blade.php

  //formの中に
  <div class="form-row"> form 内を変更
    <div class="form-group col-md-6">
      <label for="book" class="col-sm-3 control-label">Book</label>
      <input type="text" name="item_name" class="form-control">
    </div>
    <div class="form-group col-md-6">
      <label for="amount" class="col-sm-3 control-label">金額</label>
      <input type="text" name="item_amount" class="form-control">
    </div>
  </div>
  <div class="form-row">
    <div class="form-group col-md-6">
      <label for="number" class="col-sm-3 control-label">数</label>
      <input type="text" name="item_number" class="form-control">
    </div>
    <div class="form-group col-md-6">
      <label for="published" class="col-sm-3 control-label">公開日</label>
      <input type="date" name="published" class="form-control">
    </div>
  </div>
  <div class="form-row">
    <div class="col-sm-offset-3 col-sm-6">
      <button type="submit" class="btn btn-primary"> Save </button>
    </div>
  </div>

  
<!-- 本: 更新ボタン -->
<td>
  <form action="{{ url('booksedit/'.$book->id) }}" method="POST"> {{ csrf_field() }}
     <button type="submit" class="btn btn-primary"> 更新 </button>
  </form>
</td>

16 views/booksedit.blade.php

@extends('layouts.app')

@section('content')
<div class="row">
    <div class="col-md-12">
    @include('common.errors')
    <form action=" {{ url('books/update') }} " method="POST">

<!-- item_name --> 
        <div class="form-group">
            <label for="item_name">Title</label>
            <input type="text" name="item_name" class="form-control" value="{{$book->item_name}}">
        </div>

<!--/ item_name -->

<!-- item_number -->
        <div class="form-group">
            <label for="item_number">Number</label>
            <input type="text" name="item_number" class="form-control" value="{{$book->item_number}}">
        </div> <!--/ item_number -->

<!-- item_amount -->
        <div class="form-group">
            <label for="item_amount">Amount</label>
            <input type="text" name="item_amount" class="form-control" value="{{$book->item_amount}}"> 
        </div> <!--/ item_amount -->

<!-- published -->
        <div class="form-group">
            <label for="published">published</label>
            <input type="datetime" name="published" class="form-control" value="{{$book->published}}"/>
        </div> <!--/ published -->

<!-- Save ボタン/Back ボタン -->
        <div class="well well-sm">
            <button type="submit" class="btn btn-primary">Save</button>
            <a class="btn btn-link pull-right" href=" {{ url('/') }} "> Back </a>
        </div> <!--/ Save ボタン/Back ボタン -->

<!-- id 値を送信 -->
        <input type="hidden" name="id" value="{{$book->id}}" /> <!--/ id 値を送信 -->

<!-- CSRF --> {{ csrf_field() }} <!--/ CSRF -->

    </form>
    </div>
</div>

@endsection

17 routes/web.php

//update処理
Route::post('/books/update', function (Request $request) {
    //バリデーション });
    $validator = Validator::make(
        $request->all(),
        [
        'id' => 'required',
        'item_name' => 'required|min:3|max:255',
        'item_number' => 'required|min:1|max:3',
        'item_amount' => 'required|max:6',
        'published' => 'required',
        ]
    ); //バリデーション：エラー if ($validator->fails()) { return redirect('/')
    if ($validator->fails()) {
        return redirect('/')
        ->withInput()
        ->withErrors($validator);
    }


    $books = Book::find($request->id);
    $books->item_name = $request->item_name;
    $books->item_number = $request->item_number;
    $books->item_amount = $request->item_amount;
    $books->published = $request->published;
    $books->save();
    return redirect('/');
});

