---
title: "MongoDB와 Mongoose"
date: 2023-08-10
last_modified_at: 2023-08-14
excerpt: "MongoDB와 Mongoose 각각의 특징과 차이점을 알아보겠습니다."
categories:
  - MongoDB
tags:
  - [mongodb, mongoose]
---

---

<br>

# Mongoose

Node.js 기반의 MongoDB의 ODM(Object Data Modeling) 라이브러리입니다.

1. MongoDB 위에 더 높은 수준의 추상화 계층을 제공합니다.
2. Schema 기반 접근 방식을 사용하여 DB 모델을 정의합니다.
   1. 데이터 구조를 정의할 수 있습니다.
   2. 각 필드의 데이터 유형을 식별할 수 있습니다.
   3. 유효성 검사 규칙을 적용해 데이터 무결성과 일관성을 보장합니다.
3. MongoDB 작업 프로세스를 단순화 하는 다양한 기능을 제공합니다.

전통적인 SQL 데이터베이스를 위한 ORM(Object Relational Mapper)인 SQLAlchemy와 유사합니다.

<br>
<br>

# MongoDB에서의 Object Data Modeling

MongoDB와 같은 NoSQL을 사용하면 엄격한 데이터 모델에 구속되지 않고 유연하게 필드를 추가하고 제거할 수 있지만, 데이터 모델에 대한 합의가 없고 collection의 모든 document에 서로 매우 다른 필드가 포함된다면 문제가 될 수 있습니다.

## Mongoose Schema와 Model

Mongoose에서는 schema 객체를 정의하고 사용할 수 있습니다.

Schema 객체는 collection에 속한 document의 구조를 정의합니다.

아래 예시는 Mongoose에서 schema를 정의하고 함께 제공되는 Mongoose 모델을 생성합니다.

```javascript
// Mongoose
const blog = new Schema({
  title: String,
  slug: String,
  published: Boolean,
  content: String,
  tags: [String],
  comments: [{
      user: String,
      content: String,
      votes: Number
  }]
});

const Blog = mongoose.model('Blog', blog);
```

모델이 정의되었고, 이제부터 CRUD 쿼리를 수행할 수 있습니다.

```javascript
// Mongoose
const article = new Blog({
  title: 'Awesome Post!',
  slug: 'awesome-post',
  published: true,
  content: 'This is the best post ever',
  tags: ['featured', 'announcement'],
});

article.save();

Blog.findOne({}, (err, post) => console.log(post));
```

하지만 이것은 애플리케이션 범위 내에서만 존재하기 때문에, MongoDB는 schema와 model의 관계를 인식할 수 없습니다.

이 방법은 Mongoose에서만 적용되고, 다른 프로그래밍 언어에서 MongoDB와 상호작용한다면 Mongoose에서 정의한 모든 제약 조건과 모델은 의미가 없어집니다.

반대로, MongoDB Node.js 드라이버에는 개체 데이터 모델링이나 매핑 개념이 없습니다.

모델 정의 없이, 아래처럼 쿼리를 작성하면 됩니다. 만약 Mongoose 였다면, author 속성이 추가되었기 때문에 실패합니다.

```javascript
// MongoDB
db.collection('posts').insertOne({
  title: 'Better Post!',
  slug: 'a-better-post',
  published: true,
  author: 'Ado Kukic',
  content: 'This is an even better post',
  tags: ['featured'],
});
```

원한다면 Node.js 드라이버에서도 `objectmodel` 라이브러리를 이용해 모델을 정의할 수 있습니다.

```javascript
// MongoDB
function Blog(post) {
  this.title = post.title;
  this.slug = post.slug;
  ...
}
```

```javascript
// MongoDB
db.collection('posts').findOne({}).then((err, post) => {
  let article = new Blog(post);
});
```

## Schema Validation

1. Mongoose schema에 정의된 애플리케이션 수준 유효성 검사기 사용하기
2. MongoDB collection 자체에 정의된 MongoDB schema 유효성 검사를 사용하기

MongoDB schema 유효성 검사는 DB 수준에서 적용된다는 것이 큰 차이점이고, 이것은 중요합니다.

### Mongoose에서 유효성 검사

애플리케이션 계층에서 schema 유효성 검사를 시행합니다.

1. 모델을 정의함으로써 특정 collection에 삽입할 수 있는 필드와 데이터 타입을 명시적으로 알려줍니다.
   - String 타입 데이터에 다른 타입의 데이터를 삽입하려 하면 실패할 것입니다.
2. 정의된 필드의 데이터가 정의된 기준 세트와 일치하는지 확인합니다.
   - 필수 필드를 요구하기
   - 특정 필드의 최솟값, 최댓값 (길이) 보장하기
   - 정규 표현식이나 사용자 정의 로직 제공 같은 특정 유효성 검사기 추가하기

```javascript
// Mongoose
const blog = new Schema({
  title: {
      type: String,
      required: true,
  },
  slug: {
      type: String,
      required: true,
  },
  published: Boolean,
  content: {
      type: String,
      required: true,
      minlength: 250
  },
  // ...
});

const Blog = mongoose.model('Blog', blog);
```

### MongoDB Node.js 드라이버에서 유효성 검사

```javascript
// MongoDB
{
  $jsonSchema: {
    bsonType: "object",
    required: [ "tags" ],
    properties: {
      title: {
        type: "string",
        minLength: 20,
        maxLength: 80
      }
    }
  }
}
```

> [JSON Schema](https://www.mongodb.com/docs/manual/reference/operator/query/jsonSchema/?_ga=2.26178237.1698277090.1691570147-20330763.1690872525#json-schema)

MongoDB의 기본 schema 유효성 검사를 통해 추가적인 유연성을 확보할 수 있습니다.

Schema를 구현할 때, 존재하는 document에 대한 유효성 검사는 자동적으로 하지 않습니다.

유효성 검사는 삽입, 수정에만 합니다.

이미 존재하는 document들을 그냥 두고 싶다면, `validationLevel`을 변경해 DB에 들어갈 새로운 document들에만 유효성 검사를 수행합니다.

또한, `validationAcion`을 통해 유효성 검사에 실패한 document들을 삽입할 것인지에 대해서도 선택할 수 있습니다.

마지막으로, 쿼리와 함께 `bypassDocumentValidation` 옵션을 전달하여 원할 때 document의 유효성 검사를 무시할 수 있습니다.

## Populate와 Lookup

SQL의 Join 기능과 유사하게, Mongoose에서는 populate를 사용합니다.

이 기능을 통해, 동일한 DB의 여러 collection에서 원하는 document를 결합할 수 있습니다.

```javascript
// Mongoose
const user = new Schema({
  name: String,
  email: String
});

const blog = new Schema({
  title: String,
  slug: String,
  published: Boolean,
  content: String,
  tags: [String],
  comments: [{
      user: { Schema.Types.ObjectId, ref: 'User' },
      content: String,
      votes: Number
  }]
});

const User = mongoose.model('User', user);
const Blog = mongoose.model('Blog', blog);
```

```javascript
// Mongoose
Blog.updateOne({
  comments: [{ user: "12345", content: "Great Post!!!" }]
});
```

그러나 이를 실현하기 위해 작업 속도가 느려질 수 있습니다.

또한, 애플리케이션 계층에만 이 개념이 존재하기 때문에, DB 관리를 위해 이것에만 의존하면 나중에 문제가 발생할 수 있습니다.

MongoDB에서는 `$lookup`을 이용해 단일 DB 내의 collection에 대해 left outer join을 수행할 수 있습니다.

```javascript
// MongoDB
Blog.
  findOne({}).
  populate('comments.user').
  exec(function (err, post) {
      console.log(post.comments[0].user.name);
  });
```

```javascript
// MongoDB
db.collection('posts').aggregate([
  {
    '$lookup': {
      'from': 'users', 
      'localField': 'comments.user', 
      'foreignField': '_id', 
      'as': 'users'
    }
  }, {}
], (err, post) => {
  console.log(post.users);
});
```

# 결론

MongoDB는 스키마 없이 유연하게 데이터를 저장하고, 데이터 구조에 대한 제한이 적어 유연성이 높습니다.

하지만 데이터 무결성과 일관성을 보장하기 어렵습니다.

Mongoose는 스키마 기반 접근 방식으로 높은 수준의 추상화 계층에서 모델을 정의해 데이터 일관성과 무결성을 쉽게 보장할 수 있습니다.

또한 편리하며, 풍부하고 일관된 기능을 통해 손쉽게 작업 프로세스를 단순화해 생산성을 향상할 수 있습니다.

하지만 DB와 상호작용하는 오버헤드가 증가하고, 데이터 모델의 유연성이 제한됩니다.

<br>
<br>

# 참고

> [MongoDB & Mongoose: Compatibility and Comparison](https://www.mongodb.com/developer/languages/javascript/mongoose-versus-nodejs-driver/)

> [Mongoose Vs MongoDB: Similarities and Differences](https://www.scaler.com/topics/mongoose-vs-mongodb/)

> [Specify JSON Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/specify-json-schema/)

> [Data Modeling Introduction](https://www.mongodb.com/docs/manual/core/data-modeling-introduction/)

---
