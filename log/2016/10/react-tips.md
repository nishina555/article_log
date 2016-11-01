Reactでは、データの変更ができるのはstateのみで、propsは決して直接いじってはいけない。
http://qiita.com/joe-re/items/96f12dda4a62470d1d7c

POSTした後にはすぐに画面をリフレッシュしたいので、stateをいじる必要がある。
ということで、stateを持っているCommentBoxに処理を実装するのが正しい。
http://qiita.com/joe-re/items/96f12dda4a62470d1d7c

inputの属性でref="author"と指定しているところ。
こうしておくと、ReactのComponentの中でthis.refs.hogeで要素にアクセスできるようになる。
http://qiita.com/joe-re/items/96f12dda4a62470d1d7c

CommentFormのsubmitイベントが実行された時に、CommentBoxに実装されたメソッドが呼ばれるようにする。
CommentBoxからCommentFormにcallbackを渡すことでこれを実現する。
http://qiita.com/joe-re/items/96f12dda4a62470d1d7c
