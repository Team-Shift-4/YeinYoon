# [ Firebase ] 9. 채팅기능 1 - 유저 & 채팅방 collection 만들기

상태: 작성 완료
생성 일시: 2022년 11월 19일 오후 11:09
중요도: ★★☆
최종 편집 일시: 2022년 12월 9일 오전 10:15
태그: 🔥 Firebase

# 9. 채팅기능 1 - 유저 & 채팅방 collection 만들기

### 유저 컬렉션 생성

파이어베이스 콘솔에서 **user** 컬렉션을 새로 생성한 뒤 회원가입을 진행할 때마다,
해당 유저의 정보가 user 컬렉션에 저장되도록 한다.
쉬운 유저 구분을 위해 문서ID의 경우 자동할당이 아닌 uid로 지정하도록 하자.

```jsx
$("#register").click(()=>{
        var name = $("#name-new").val();
        var email = $("#email-new").val();
        var password = $("#pw-new").val();

        firebase.auth().createUserWithEmailAndPassword(email,password)
        .then(async(result)=>{

          var userData = {
            name : name,
            email : email
          }
          await db.collection('user').doc(result.user.uid).set(userData);

          console.log(result);
          console.log(result.user)
          result.user.updateProfile({displayName : name})
          alert("가입완료");
          window.location.href = "/signup.html";
       })

 })
```

회원가입을 진행하는데 **permission(권한) 에러**가 발생하거나 user 데이터가 저장되지 않을 경우,
user 컬렉션에 대한 규칙이 정해져 있지 않기에 발생하는 문제이다.
다음과 같은 코드를 파이어베이스 콘솔의 규칙에 기입해준다.

```java
match /user/{docid} {
    	allow read, write : if true;
}
```

규칙을 수정 후에는 유저 데이터가 컬렉션에 제대로 저장되는 것을 확인할 수 있다.

### Q. 왜 user 컬렉션을 새로 만들어서 유저 데이터를 따로 저장함?

**A.** 여태까지 우리는 회원가입 한번 해두면 유저 정보를 파이어베이스 서버에 요청해서 데이터를 받아보는 것으로 실습을 진행했는데, 여기서 헷갈릴 수 있는 부분은 가입정보가 firestore에 바로 저장된다는 착각이다.

하지만 DB에 가입한 정보가 저장되고 우리는 그 데이터를 **직접 요청**하지 않는 이상 바로 볼 수 없다.
따라서 유저 정보를 다루기 쉽게하기 위해, 컬렉션을 새로 생성하고 가입이 완료된 유저의 데이터를 미리 저장해두면 코딩이 편하다는 논리에서 제안하는 방법이다.

그게 아니더라도 어차피 서비스 개발하면서 유저에 대한 추가정보를 저장할 일이 많기 때문에 user 컬렉션에 넣어두면 편하게 써먹을 수 있어서 끝임

### 채팅방 컬렉션 생성, 채팅방 개설

채팅방을 개설하기 위한 chatroom 컬렉션을 새로 생성한다.
채팅 버튼을 클릭했을 때, 참여하는 유저들의 정보를 가지고 채팅방을 개설하는 코드를 작성한다.

```jsx
		const db = firebase.firestore();
    const storage = firebase.storage();

    // 내 uid, 채팅을 걸 상대방의 uid, 상품 정보
    var currentUid = JSON.parse(localStorage.getItem('user')).uid;
    var chatUid;
    var productName;
    
    //URL 쿼리스트링 객체
    var queryString = new URLSearchParams(window.location.search);
    
    db.collection("product").doc(queryString.get("id")).get() // 상세페이지에 해당 상품의 정보를 읽어들일때 uid를 저장한다.
    .then((result)=>{

      // 채팅방 개설을 위한 상대방 uid, 상품 제목 저장
      chatUid = result.data().uid;
      productName = result.data().제목;

      console.log(result.data());
      $(".user").text(result.data().이름);
      $(".title").text(result.data().제목);
      $(".date").text(result.data().날짜);
      $(".price").text(result.data().가격);
      $(".content").text(result.data().내용);
      $('.detail-pic').css('background-image', `url(${result.data().이미지})`);
        
    })

    $('#edit').click(()=>{

      var user = JSON.parse(localStorage.getItem('user'));

      if(localStorage.getItem('user') == null) { // 로그인하지 않았을 경우
        alert("로그인을 진행해주세요");
      } else if(user.displayName == $(".user").text()) { // 로그인하였고 작성자가 맞을 경우
        console.log(user.displayName);
        window.location.href = `/edit.html?id=${queryString.get("id")}`
      } else { // 로그인은 하였지만 글 작성자 본인이 아닐경우
        alert("권한이 없습니다.")
      }

    })

    $("#chat").click(()=>{ // 채팅방 생성
      var data = {
        who : [currentUid, chatUid], // 채팅에 참여하는 사람
        product : productName, // 상품명
        date : new Date() // 채팅방이 개설된 날짜
      }

      db.collection('chatroom').add(data);
    })
```

채팅방 HTML을 아래와 같이 작성해보자. (chat.html)

```html
<div class="container p-4 detail">
    <div class="row">
      <div class="col-3 p-0">
        <ul class="list-group chat-list">
          <li class="list-group-item">
            <h6>채팅방1</h6>
            <h6 class="text-small">채팅방아이디</h6>
          </li>
        </ul>
      </div>
      <div class="col-9 p-0">
        <div class="chat-room">
          <ul class="list-group chat-content">
            <li><span class="chat-box">채팅방1 내용</span></li>
            <li><span class="chat-box">채팅방1 내용</span></li>
            <li><span class="chat-box mine">채팅방1 내용</span></li>
          </ul>
          <div class="input-group">
            <input class="form-control" id="chat-input">
            <button class="btn btn-secondary" id="send">전송</button>
          </div>
        </div>
      </div>
    </div>
 </div>
```

여기까지 작성이 끝났다면, 다음 챕터에서 파이어베이스에서 제공하는 쿼리를 이용해 원하는 문서만 가져오는 작업을 진행해볼 것이다. ( **ex) 내가 소속된 채팅방 목록을 가져오는 쿼리** )