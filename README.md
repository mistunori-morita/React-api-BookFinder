# React-BookFineder
## 初期セットアップ
- `npm install --save axios react-bootstrap`
- `https://bootswatch.com/`でテーマサンプルを利用
- ダウンロードを押してURL部分をコピー
- bootstrapのcdnをコピーしてjsを貼り付け`https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js`
- jqueryのcdnをコピーして貼り付け
- public/index.htmlに全て記述
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <link rel="stylesheet" href="https://bootswatch.com/4/journal/bootstrap.min.css">
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <title>React App</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
    <script
  src="https://code.jquery.com/jquery-3.2.1.js"
  integrity="sha256-DZAnKJ/6XZ9si04Hgrsxu/8s717jcIzLy3oi35EouyE="
  crossorigin="anonymous"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  </body>
</html>
```
- npm startで起動したらok

## 不要なパーツの削除
- svg,jsのスクリプト（初期設定されているもの削除）
- 初期cssも削除

## Componentsを作成
- components/Books.js
- components/Header.js
- components/SearchInput.js

```js
//Header.js
import React, { Component } from 'react';
import { Navbar } from 'react-bootstrap';

class Header extends Component {
  render() {
    return (
      <Navbar>
        <Navbar.Header>
          <Navbar.Brand>
            BookFinder
          </Navbar.Brand>
        </Navbar.Header>
      </Navbar>
    );
  }
}

export default Header;
```

```js
//Books.js
import React, { Component } from 'react';
import { Navbar } from 'react-bootstrap';

class Books extends Component {
  render() {
    return (
      <div>
        Books
      </div>
    );
  }
}

export default Books;

```

```js
//App.jsにインポート

import React, { Component } from 'react';
import {Grid, Col, Row} from 'react-bootstrap';
import Header from './components/Header';
import Books from './components/Books';
import './App.css';

class App extends Component {
  constructor(){
    super();
    this.state ={
      books: [],
      text: 'Harry Potter'
    }
  }

  render() {
    return (
      <div className="App">
        <Header />
        <Grid>
          <Row>
            <Col xs={12} md={12} lg={12}>
              <Books />
            </Col>
          </Row>
        </Grid>
      </div>
    );
  }
}

export default App;

```

## GoogleBookAPIを取得
- axiosを使ってapiと通信
```js
//App.js

import axios from 'axios';

//constructorでstateを定義
constructor(){
  super();
  this.state ={
    books: [],
    text: 'Harry Potter'
  }
}

componentWillMount(){
  this.getBooks();
}


//getBooksの中でaxios通信を実行 responseの中にapi情報が入ってくるので、それをsetStateでbooksの配列に格納
getBooks(){
  axios.request({
    method: 'get',
    url:'https://www.googleapis.com/books/v1/volumes?q='+this.state.text
  }).then((response) => {
    this.setState({books: response.data.items}, () => {
      console.log(this.state);
    })
  }).catch((error) => {
    console.log(error);
  })
}


//Booksコンポーネントにstateのbooksを渡す
<Books books={this.state.books}/>

//この段階でconsole.logにobjectで見れるとOK これをチューニングしていく
```
- react-boot-strapのコンポーネントのアコーディオンを使って実装をしていく
- https://react-bootstrap.github.io/components.html#panels-accordion
```js
//Books.js
import React, { Component } from 'react';
//Accordion,Panelを追加
import { Grid, Row, Col, Accordion, Panel } from 'react-bootstrap';

class Books extends Component {
  render() {
    //変数を準備
    let bookItems;

    //if文でpropsでbooksが渡ってきたら下の処理を行うロジック
    if(this.props.books){
      //bookItemsにbooksをmapで格納
      bookItems = this.props.books.map(book => {
        //apiの情報を紐づけて格納
        let id = book.id;
        let title = book.volumeInfo.title;
        return (
          <Panel key={id} header={title} eventKey={id}>
            test
          </Panel>
        )
      });
    }

    return (
      <div>
        <Accordion>
        //条件分けした変数名を展開
          {bookItems}
        </Accordion>
      </div>
    );
  }
}

export default Books;
//これで問題なければアコーディオンが動作している
```

## BookDataの出力＋スタイリング
- Books.jsを作り込む
```js
import React, { Component } from 'react';
//ListGroup, ListGroupItem, Button を読み込み
import { Grid, Row, Col, Accordion, Panel, ListGroup, ListGroupItem, Button } from 'react-bootstrap';

class Books extends Component {
  render() {

    let bookItems;
    if(this.props.books){
      bookItems = this.props.books.map(book => {
        let id = book.id;
        let title = book.volumeInfo.title;
        //apiの情報に紐づいて取れるのでそれを変数に格納
        let thumbnail = book.volumeInfo.imageLinks.thumbnail;
        let categories = book.volumeInfo.categories;
        let authors = book.volumeInfo.authors;
        let publisher = book.volumeInfo.publisher;
        let description = book.volumeInfo.description;
        let pageCount = book.volumeInfo.pageCount;
        let publisheDate = book.volumeInfo.publisheDate;
        let averageRating = book.volumeInfo.averageRating;
        let buyLink = book.saleInfo.buyLink;


        return (
          <Panel key={id} header={title} eventKey={id}>
            <Grid>
              <Row>
                <Col xs={3} md={3} lg={3}>
                  <img src={thumbnail} role="presentation"/>
                </Col>
                <Col xs={8} md={8} lg={8}>
                //各表示したい場所に、先ほど上で格納したものを適用させる
                  <ListGroup>
                    <ListGroupItem><strong>Categories:</strong>{categories}</ListGroupItem>
                    <ListGroupItem><strong>Authors:</strong>{authors}</ListGroupItem>
                    <ListGroupItem><strong>Publisher:</strong>{publisher}</ListGroupItem>
                    <ListGroupItem><strong>Publish Date:</strong>{publisheDate}</ListGroupItem>
                    <ListGroupItem><strong>Page Count:</strong>{pageCount}</ListGroupItem>
                    <ListGroupItem><strong>Average Rating:</strong><span className="rating">{averageRating}</span></ListGroupItem>
                  </ListGroup>
                </Col>
              </Row>
              <Row>
                <Col xs={11} md={11} lg={11}>
                  <h3>Book Description</h3>
                  {description}
                  <hr/>
                  <Button href={buyLink} bsStyle="primary">Buy Now</Button>
                </Col>
              </Row>
            </Grid>
          </Panel>
        )
      });
    }

    return (
      <div>
        <Accordion>
          {bookItems}
        </Accordion>
      </div>
    );
  }
}

export default Books;

```

## 検索入力から利用できるようにする
- SearchInputを作り込む
```js
//App.js
//importする
import SearchInput from './components/SearchInput';

//stateがconstructorのtextに格納
<SearchInput value={this.state.text}/>
```

```js
//SearchInput.js

import React, { Component } from 'react';
import { Well, FormControl } from 'react-bootstrap';

class SearchInput extends Component {
  //まずappから渡ってくるpropsをthis.stateにする
  constructor(props){
    super(props);
    this.state = {
      value: props.value
    }
  }

  //onChangeイベントでsetStateでthis.stateのvalueを更新
  onChange(e){
    this.setState({value: e.target.value});
    //propsでonChangeイベントに入ってくるstateを設定
    this.props.onChange(this.state.value);
  }


  render() {
    return (
      <Well>
        <FormControl
        type="text"
        //valueはprops.valueで設定されたものが入ってくる
        value={this.state.value}
        plachehoolder="Search Books...."
        //onChangeで入力されたらonChangeイベントが走るようにする
        onChange={this.onChange.bind(this)}/>
      </Well>
    );
  }
}

export default SearchInput;

```
- App.jsを編集
```js

  //eventを設定 textが入ってくるので、それをsetStateで更新して、getBooksを実行
  handleChange(text){
    this.setState({text: text}, this.getBooks());
  }

  render() {
    return (
      <div className="App">
        <Header />
        <Grid>
          <Row>
            <Col xs={12} md={12} lg={12}>
            //eventの設定
              <SearchInput onChange={this.handleChange.bind(this)} value={this.state.text}/>
              <Books books={this.state.books}/>
            </Col>
          </Row>
        </Grid>
      </div>
    );
  }

```
