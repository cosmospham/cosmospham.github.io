---
layout: post
title: "Promise là cái khỉ gì?"
date: 2016-04-16 14:00:03 +0700
categories: javascript
description: "Bài viết này sẽ giới thiệu cho các bạn một đối tượng torng JavaScript, đó là Promise. Nghe khá mới mẻ phải không nào."
image: "javascript-promise-la-gi.jpg"
---
Bài viết này sẽ giới thiệu cho các bạn một đối tượng trong JavaScript, đó là `Promise`. Nghe khá mới mẻ phải không nào.

Để dễ hình dung, mình sẽ lấy `fetch` làm ví dụ. `fetch` là một function nằm dưới trướng của `window`. Ngày xưa ta dùng AJAX để GET một URL nào đó, thông qua API là `XMLHttpRequest`, hãy xem thử ví dụ sau:

{% highlight javascript %}
function reqListener () {
  console.log(this.responseText);
  // in ra một nùi HTML thể hiện trang chủ của mình
}

var oReq = new XMLHttpRequest();
oReq.addEventListener("load", reqListener);
oReq.open("GET", "https://cosmospham.github.io");
oReq.send();
{% endhighlight %}

Với `fetch`, nó viết có phần gọn và sáng sủa hơn:

{% highlight javascript %}
fetch("https://cosmospham.github.io", {
    // config
    method: 'get'
}).then(function(response) {
    // mừng quá có response rồi
    console.log(response);
    // in ra cái response object
}).catch(function(err) {
    // thế éo nào lại lỗi :v
    console.log(err);
});
{% endhighlight %}

Nói nảy giờ vậy Promise đâu? Nó là chính cái mà hàm `fetch()` trả về. Vì chúng ta không biết được kết nối Internet nhanh hay chậm, không biết cái trang github page kia nặng hay nhẹ, mất bao lâu để tải xong, cho nên phải CHỜ. Chờ đến khi tải xong thì mới `then` để xử lý cái `response` nhận được.

Nhưng giờ tui cần cái HTML text như ví dụ đầu tiên kìa, ngon in ra coi. Ờ, in thì in:

{% highlight javascript %}
fetch("https://cosmospham.github.io", {
    // config
    method: 'get'
}).then(function(response) { // gọi đây là then_1
    // mừng quá có response rồi
    // chú ý chỗ return này nhé
    return response.text();
}).then(function(htmlText) { // then_2
    console.log(htmlText);
    // nè, thích thì chiều
}).catch(function(err) {
    // thế éo nào lại lỗi :v
    console.log(err);
});
{% endhighlight %}

Ở chỗ `then_1`, hàm `text()` của thằng `response` trả về một promise nữa. Để làm chi? Vì khi get cái trang github page về, mình muốn đọc HTML của nó thì cần một thời gian nữa để xử lý ĐỌC. Khi đọc xong, promise sẽ được chạy. Và cái `then_2` được thực thi, nhận tham số là `htmlText` - tức cái mà hàm `text()` xử lý xong trả về.

Tạm dừng ở đây để nói về JavaScript chút. JavaScript nó không giống C# hay Java ở chỗ chạy xong dòng 1 rồi mới tới dòng 2. Nó không chờ get xong cái URL về rồi mới lấy HTML text ra. Hãy chạy thử ví dụ sau:

{% highlight javascript %}
console.log('Đây là bắt đầu.');

setTimeout(function() {
    console.log('Tui đang ở trong hàm setTimeout');
    console.log('Hàm setTimeout được gọi nè.');
}, 2000);

console.log('Đây là kết thúc.');
console.log('Kết thúc phải nằm sau cùng nhé.');
{% endhighlight %}

Ai đời kết thúc lại ra trước hàm setTimeout!? Bản tính của JavaScript là vậy đó, nó chẳng chờ đợi ai cả. Cho nên chúng ta cần Promise là vậy. Thử viết một Promise xử lý đoạn code trên nhé:

{% highlight javascript %}
var timeout = {
    run: function(time) {
        var promise = new Promise(function(resolve, reject) {
            console.log('Đây là bắt đầu.');
            setTimeout(function() {
                // đây là một mớ hành động nào đó chiếm vài chục mili giây,
                // hay vài giây mà bạn chẳng biết nó sẽ xong lúc nào.
                console.log('Tui đang ở trong hàm setTimeout');
                console.log('Hàm setTimeout được gọi nè.');
                resolve();
            }, time);
        });

        return promise; // nhớ return để còn xài tiếp
    }
}


timeout.run(5000).then(function() {
    // cái mớ này sẽ được hàm resolve() gọi
    // như ví dụ này thì gọi sau 2 dòng console.log() trong cái setTimeout
    console.log('Đây là kết thúc.');
    console.log('Kết thúc phải nằm sau cùng nhé.');
});
{% endhighlight %}

Trong quá trình xử lý, như get cái URL, mình nhận được response, vậy làm sao lấy ra xài trong `then`?

{% highlight javascript %}
var timeout = {
    run: function(time) {
        var promise = new Promise(function(resolve, reject) {
            console.log('Đây là bắt đầu.');
            setTimeout(function() {
                // đây là một mớ hành động nào đó chiếm vài chục mili giây,
                // hay vài giây mà bạn chẳng biết nó sẽ xong lúc nào.
                console.log('Tui đang ở trong hàm setTimeout');
                console.log('Hàm setTimeout được gọi nè.');

                // trong quá trình thực hiện, có biến mới nào đó
                time_2 = time*2;
                // bỏ nó vô resolve để then() gọi ra xài
                resolve(time_2);
            }, time);
        });

        return promise; // nhớ return để còn xài tiếp
    }
}


timeout.run(5000).then(function(time) {
    // cái mớ này sẽ được hàm resolve() gọi
    // như ví dụ này thì gọi sau 2 dòng console.log() trong cái setTimeout
    console.log('Đây là kết thúc.');
    console.log('Kết thúc phải nằm sau cùng nhé.');

    console.log('Mình mới lấy được biến mới nè, nó có giá trị: ' + time);
});
{% endhighlight %}

Nhìn thử nó cũng na ná cái `fetch()` rồi ha. Giờ phát triển đoạn code trên để dùng thêm 1 cái `then` nữa nha:

{% highlight javascript %}
var timeout = {
    run: function(time) {
        var promise = new Promise(function(resolve, reject) {
            console.log('Đây là bắt đầu, với ' + time);
            setTimeout(function() {
                // đây là một mớ hành động nào đó chiếm vài chục mili giây,
                // hay vài giây mà bạn chẳng biết nó sẽ xong lúc nào.
                console.log('Tui đang ở trong hàm setTimeout');
                console.log('Hàm setTimeout được gọi nè.');

                // trong quá trình thực hiện, có biến mới nào đó
                time_2 = time*2;
                // bỏ nó vô resolve để then() gọi ra xài
                resolve(time_2);
            }, time);
        });

        return promise; // nhớ return để còn xài tiếp
    }
}

timeout.run(2000).then(function(time) {
    // cái mớ này sẽ được hàm resolve() gọi
    // như ví dụ này thì gọi sau 2 dòng console.log() trong cái setTimeout
    console.log('Đây là kết thúc.');
    console.log('Kết thúc phải nằm sau cùng nhé.');

    console.log('Mình mới lấy được biến mới nè, nó có giá trị: ' + time);

    return timeout.run(time);
}).then(function(new_time) {
    console.log('Mệt rồi nha, biến mới nè: ' + new_time);
});
{% endhighlight %}

Thử comment dòng `return promise;` ở trên lại xem, nó báo `Uncaught TypeError: Cannot read property 'then' of undefined`. Như vậy, cái `then` là prototype của object Promise, đó là lý do ta cần return để "`then`".

Đoạn code sau có thể viết lại một chút, kết quả vẫn như cũ:

{% highlight javascript %}
var timeout = {
    // như code cũ
}

// khúc này sửa nè
result = timeout.run(2000).then(function(time) {
    // cái mớ này sẽ được hàm resolve() gọi
    // như ví dụ này thì gọi sau 2 dòng console.log() trong cái setTimeout
    console.log('Đây là kết thúc.');
    console.log('Kết thúc phải nằm sau cùng nhé.');

    console.log('Mình mới lấy được biến mới nè, nó có giá trị: ' + time);

    return timeout.run(time);
});

result.then(function(new_time) {
    console.log('Mệt rồi nha, biến mới nè: ' + new_time);
});
{% endhighlight %}

Như vậy, qua vài đoạn code trên các bạn cũng hiểu được Promise là gì và cách tạo, sử dụng Promise rồi ha. Hẹn gặp ở các bài viết khác. &copy;