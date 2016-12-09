> Work in progress

#  GAMALIEL - Building Your First Clean Architecture Code using Python - A Step by step Example

## Gambaran





## Apa itu Clean Architecture ?

[The Clean Architecture](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html) post dari Uncle Bob

[Clean Architectures in Python a step by step example]([http://blog.thedigitalcatonline.com/blog/2016/11/14/clean-architectures-in-python-a-step-by-step-example]) oleh Leonardo Giordani

Tujuannya adalah agar setiap lapisan sifatnya independent dan testable

> #### Separation of Concerns
>
> It **emphasizes the use of interfaces for behavior contracts**, and it forces **the externalization of infrastructure** - http://jeffreypalermo.com/blog/the-onion-architecture-part-1/



> #### Testable Architectures
>
> If you system architecture is all about the use cases, and if you have kept your frameworks at arms-length. Then **you should be able to unit-test all those use cases** without any of the frameworks in place. You shouldn’t need the web server running in order to run your tests. You shouldn’t need the database connected in order to run your tests. Your business objects should be plain old objects that have no dependencies on frameworks or databases or other complications. Your use case objects should coordinate your business objects. And all of them together should be testable in-situ, without any of the complications of frameworks. -  https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html



## Apa yang akan kita buat?

Kita akan membuat simple RESTful API menggunakan clean architecture

## Apa saja yang dibutuhkan?

* Python 3.5

* PyCharm atau editor favorit

* [VirtualEnv](http://docs.python-guide.org/en/latest/dev/virtualenvs/)

* [Flask](http://flask.pocoo.org/)

* [Pytest](http://doc.pytest.org/en/latest/)

  ​



## Gambaran Projek

Goal dari projek **Article-o-matic** adalah membuat aplikasi untuk mengelola article menggunakan RESTful API.

Supaya tetap sederhana kita hanya menggunakan 1 object, yaitu object `Article`  yang terdiri dari :

* `article_id` (string) - ID dari artikel
* `title` (string) - Judul Artikel
* `content` (string) - Isi Artikel
* `published_at` (timestamp) - Waktu Publikasi dalam bentuk unix timestamp

Karena kita menggunakan clean architecture model, maka kita harus memisahkan aplikasi ini ke dalam beberapa layer (lapisan). 

### Entities

Lapisan ini dimana domain model atau proses bisnis kita berada. Kita akan membuat `class` yang merepresentasikan `Article` dengan data yang tersimpan di dalam `database` 

### Use Cases

`Usecase` dapat diibaratkan sebagai kumpulan dari skenario-skenario yang berhubungan untuk mencapai tujuan tertentu. `Usecase` terdiri dari semua aktivitas yang di lakukan oleh user. 

> The reason that good architectures are centered around use-cases is so that architects can safely describe the structures that support those use-cases **without committing to frameworks, tools, and environment** - https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html

Beberapa use cases yang akan kita buat adalah.

1. `StoreUsecase` - Usecase ini digunakan untuk menyimpan artikel baru 
2. `GetDetailUsecase` - Usecase ini digunakan untuk mendapatkan informasi dari artikel
3. `DeleteUsecase` - Usecase ini digunakan untuk menghapus artikel
4. `UpdateUsecase` - Usecase ini digunakan untuk memperbaharui informasi di dalam artikel



### Repository

Lapisan Repository menjembatani antara domain dan lapisan data mapping. Lapisan data mapping dapat berupa : RDBMS, NoSQL, File, API, dll. Sehingga implementasi dari repository dapat bermacam-macam.

Beberapa fungsi dalam repository yang akan kita buat adalah.

1. `Store`
2. `Get`
3. `Delete`
4. `Update`


### Delivery

> **Your system architecture should be as ignorant as possible about how it is to be delivered**. You should be able to deliver it as a console app, or a web app, or a thick client app, or even a web service app, without undue complication or change to the fundamental architecture. - https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html




## Struktur Projek

Projek **article-o-matic** terdiri dari :

`src`

|—`article`

|——`repository`

|———`__init__.py`

|—`tests`

|—--`__init__.py`

`setup.cfg`



Entities, Usecase akan berada di dalam folder `article`.

Repository contract/interface akan berada dalam folder `repository`



## Initialization Project

Install **virtualenv** `pip3 install virtualenv`

Pindah direktori ke project `article-o-matic`

Buat virtualenv dalam project tersebut `virtualenv venv`

Untuk memulai virtualenv masukkan command `source venv\bin\activate`

**Pertanyaannya, Kenapa kita menggunakan Virtual Environments (venv) ?**

`venv` digunakan untuk menyimpan dependencies yang dibutuhkan oleh beberapa project dalam tempat yang terpisah, dengan membuat environment virtual Python. `venv` menyelasaikan masalah "Project X bergantung pada versi 1.x, namun Project Y bergantung pada 4.x"

Install **pytest** `pip3 install pytest`

Install **Flask** `pip3 install flask`

Save dependencies nya. `pip3 freeze > requirements.txt` sehingga di lain waktu kita dapat menginstall dependencies nya dengan command `pip3 install -r requirements.txt`



## Entities

Buat file `test_article.py` di folder `tests/`

```python
from article import Article

def test_article_attributes():
 	article = Article(article_id='1234', title='this is title', content='this is content', published_at=1479885808)  
    
    assert article.article_id == '1234'
    assert article.title == 'this is title'
    assert article.content == 'this is content'
    assert article.published_at == 1479885808
    	
 def test_article_to_dict():
	expected_article_dict = {
      'article_id': '1234',
      'title': 'this is title',
      'content': 'this is content',
      'published_at':   1479885808
	}

	article = Article(article_id='1234', title='this is title', content='this is content', published_at=1479885808)  
    
    assert expected_article_dict == article.to_dict()
    
```

Kedua test diatas memastikan entity kita dapat di inisiasi dengan nilai yang tepat dan dapat di convert ke dictionary

Jalankan `pytest —ignore=venv`, muncul red status bukan? Berikutnya kita akan membuat class Article dan memastikan testnya hijau. Buka file `src/article/__init__.py`

```python 
class Article(object):

	def __init__(self, article_id, title, content, published_at):
		self.article_id = article_id
		self.title = title
		self.content = content
		self.published_at = published_at
	
	def to_dict(self):
	    return {
           'article_id': self.article_id,
           'title': self.title,
           'content': self.content,
           'published_at': published_at
	    }
```

Jalankan `pytest —ignore=venv`, muncul green status



## Repository

```python
class Repository(object):

    def store(self, article):
        """
        Store an article

        Args:
           article (src.article.Article)

        Returns:
           src.article.Article
        """
    	raise NotImplementedError
    
    
    def get(self, article_id):
        """
        Get an article by article_id

        Args:
           article_id (str)

        Returns:
           src.article.Article
        """
    	raise NotImplementedError  
    
    
    def delete(self, article):
      	"""
      	Delete article from storage

      	Args:
          article (src.article.Article)

      	Returns:
          None
      	"""
    	raise NotImplementedError  

    
	def update(self, article):
        """
        Update article 

        Args:
            article (src.article.Article)

        Returns:
            (src.article.Article)
        """
    	raise NotImplementedError  
```



Kode di atas adalah kontrak yang harus di implement setiap class yang menggunakan repository. Implementasinya akan kita buat nanti.



### Usecases

`src/tests/test_store_article.py`

```python
from article.store import StoreUsecase
from article.exceptions import ValidationError
from unittest import mock
import pytest


@pytest.fixture
def usecase():
    repository = mock.Mock()
    store_usecase = StoreUsecase(repository=repository)
    return store_usecase

def test_store_usecase(usecase)
    usecase.repository.store.return_value = mock.Mock()
    data = {
        'title': 'this is title',
        'content': 'this is content',
        'published_at': 1479888658 
	}
	assert usecase.store(data) is not None
    assert usecase.repository.store.call_count == 1

def test_store_invalid_data(usecase)
	usecase.repository.store.return_value = None
    data = {
        'title': '',
        'content': 'this is content',
        'published_at': 1479888658 
	}
    with pytest.raises(ValidationError):
    	usecase.store(data)   
    usecase.repository.store.assert_not_called()
```



`test_update_article.py`

```python
from article.update import UpdateUsecase
from article.exceptions import ValidationError
from unittest import mock
import pytest


@pytest.fixture
def usecase():
    repository = mock.Mock()
    update_usecase = UpdateUsecase(repository=repository)
    return update_usecase

def test_update(usecase):
    article_id = '123'
    data = {
      	'title': 'updated title',
        'content': 'updated content',
        'published_at': 1479888658
    }
    usecase.repository.get.return_value = mock.Mock()
    usecase.update(article_id=article_id, data=data)
    usecase.repository.get.assert_called_once_with(article_id)
    usecase.repository.update.assert_called_once()

def test_update_non_exists_article(usecase):
    article_id = 'nonexistid'
    data = {
      	'title': 'updated title',
        'content': 'updated content',
        'published_at': 1479888658
    }
    usecase.repository.get.return_value = mock.Mock()
	usecase.update(article_id=article_id, data=data)
    usecase.repository.get.assert_called_once_with(article_id)
    usecase.repository.update.assert_not_called()
    
    
def test_update_with_invalid_title(usecase):
    article_id = '123'
    data = {
      	'title': 123456,
        'content': 'updated content',
        'published_at': 1234567
    }
    
    with pytest.raises(ValidationError):
		usecase.update(article_id=article_id, data=data)
	
    usecase.repository.get.assert_not_called()
	usecase.repository.update.assert_not_called()

    
def test_update_with_invalid_content(usecase):
    article_id = '123'
    data = {
      	'title': 'updated title',
        'content': 12345,
        'published_at': 1234567
    }
    
    with pytest.raises(ValidationError):
		usecase.update(article_id=article_id, data=data)
	
    usecase.repository.get.assert_not_called()
	usecase.repository.update.assert_not_called()

    
def test_update_with_invalid_published_at(usecase):
    article_id = '123'
    data = {
      	'title': 'updated title',
        'content': 'updated content,
        'published_at': 'itshouldbeinteger'
    }
    
    with pytest.raises(ValidationError):
		usecase.update(article_id=article_id, data=data)
	
    usecase.repository.get.assert_not_called()
	usecase.repository.update.assert_not_called()

```

`test_delete_article.py`

```python
from article.delete import DeleteUsecase
from article.exceptions import ArticleNotFound
from unittest import mock
import pytest


@pytest.fixture
def usecase():
    repository = mock.Mock()
    delete_usecase = DeleteUsecase(repository=repository)
    return delete_usecase

def test_delete(usecase):
    article_id = '123'
    usecase.delete(article_id=article_id)
    usecase.repository.get.assert_called_once_with(article_id)
	usecase.repository.delete.assert_called_once()

def test_delete_non_exists_article(usecase):
    article_id = 'nonexists'
    usecase.repository.get.return_value = None
    
    with pytest.raises(ArticleNotFound):
    	usecase.delete(article_id=article_id)
        
    usecase.repository.get.assert_called_once_with(article_id)
	usecase.repository.delete.assert_not_called()
```

`test_get_detail_article.py`

```python
from article.get import GetDetailUsecase
from article.exceptions import ArticleNotFound
from unittest import mock
import pytest


@pytest.fixture
def usecase():
    repository = mock.Mock()
    get_usecase = GetDetailUsecase(repository=repository)
    return get_usecase

def test_get_detail(usecase):
    article_id = '123'
    usecase.repository.get.return_value = mock.Mock()
    assert usecase.get_detail(article_id=article_id) is not None
    usecase.repository.get.assert_called_once_with(article_id)

def test_get_detail_non_exists_article(usecase):
    article_id = 'nonexist'
    usecase.repository.get.return_value = None
    
    with pytest.raises(ArticleNotFound):
    	usecase.get_detail(article_id=article_id)
        
    usecase.repository.get.assert_called_once_with(article_id)


```





## HTTP Server



```python
import traceback
from http import HTTPStatus
from flask import Flask, request, jsonify, redirect
from flask.helpers import make_response
from article.repository.dummy import DummyRepository
from article.exceptions import ArticleNotFound
from article.get import GetDetailUsecase

app = Flask(__name__)

repository = DummyRepository()
get_usecase = GetDetailUsecase(repository=repository)

@app.route('/article/<article_id>', methods=['GET'])
def get_article(article_id):
    try:
        article = get_usecase.get(article_id)
        response = article.to_dict()
        status = HTTPStatus.OK
    except ArticleNotFound as e:
    	status = HTTPStatus.NOT_FOUND
        response = generate_error(e)
        
    return make_response(jsonify(response), status)

def generate_error(e):
    response = {"error": str(e)}
    response["traceback"] = traceback.format_exc()
    return response

if __name__ == "__main__":
    host = '127.0.0.1'
    port = 7575
    app.run(host=host, port=port)
```



Bagaimana bila kita mempunyai lebih dari satu atau puluhan usecase?

Solusinya adalah kita membuat satu kelas yang menampung inisiasi config, repository dan use case. Kita namakan kelas ini Container

`src/container.py`

```python
from article.repository import Repository
from article.get import GetDetailUsecase


class Container:
    
    self._article_repository = None
    
    @property
    def article_repository(self)
    	if self._article_repository is None:
            self._article_repository = Repository()
    	return self._article_repository        
    
    @article_repository.setter
    def article_repository(self, value)
    	self._article_repository = value
        
    @property
    def get_article_usecase(self):
        if self._get_article_usecase is None:
            self._get_article_usecase = GetDetailUsecase(repository=self.article_repository)
            
    @get_article_usecase.setter        
    def get_article_usecase(self, value)
    	self.__get_article_usecase = value
```



Mari kita refactor `src/http_server.py`

```python
import traceback
from http import HTTPStatus
from flask import Flask, request, jsonify, redirect
from flask.helpers import make_response
from container import Container

app = Flask(__name__)

container = Container()

@app.route('/article/<article_id>', methods=['GET'])
def get_article(article_id):
    try:
        article = container.get_usecase.get(article_id)
        response = article.to_dict()
        status = HTTPStatus.OK
    except ArticleNotFound as e:
    	status = HTTPStatus.NOT_FOUND
        response = generate_error(e)
        
    return make_response(jsonify(response), status)

def generate_error(e):
    response = {"error": str(e)}
    response["traceback"] = traceback.format_exc()
    return response

if __name__ == "__main__":
    host = '127.0.0.1'
    port = 7575
    app.run(host=host, port=port)
```



Dan kita dapat dengan mudah membuat test case untuk http_server

`src/tests/test_http_server.py`

```python
from container import Container
from unittest import mock

import http_server
import json
import pytest


@pytest.fixture
def container():
    http_server.container = Container()

    return http_server.container

@pytest.fixture
def app():
    return http_server.app.test_client()

def test_get_article(container, app):
    out_params = {
        'article_id': '1234',
      	'title': 'this is title',
      	'content': 'this is content',
      	'published_at': 1479885808
    }

    result = mock.Mock()
    result.to_dict.return_value = out_params

    container.get_usecase = mock.Mock()
    container.get_usecase.get.return_value = result

    response = app.get('/article/1234')

    assert response.status_code == 200
    assert json.loads(response.get_data(as_text=True)) == out_params
```

