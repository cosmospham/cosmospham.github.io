---
title: "Cách xóa cache, cập nhật Facebook Open Graph cho trang web"
layout: post
tags: ["facebook", "open graph", "cache"]
category: facebook
description: "Hướng dẫn xóa cập nhật description, title, hình ảnh khi chia sẻ link trang web lên Facebook"
image: "open-graph.png"
---
Như bạn đã biết (có khi éo biết) là nếu bạn chia sẻ một link lên Facebook, Facebook nó hiện ra hình ảnh trong (hoặc cấu hình trong) trang web, tiêu đề (title) và phần mô tả (description). Trong lần đầu tiên chia sẻ link, Facebook sẽ lưu các thông tin trên lại (cache) để lần sau load cho lẹ.

Lỡ bạn hay ai đó chia sẻ link một (vài) lần rồi, giờ bạn sửa bài viết, trang web lại và nội dung mới không giống với cái mà Facebook đã lưu cache, và Facebook cứ load trong cache ra mãi (tới khi cache expired, nếu có).

Cách sau đây sẽ giúp bạn bắt Facebook xóa cache đi và load nội dung mới về. Bạn vào link [Open Graph Object Debugger](https://developers.facebook.com/tools/debug/og/object/), điền link trang web của bạn cần xóa cache vào ô `Input URL`, sau đó bấm nút `Fetch new scrape information`.

![Open Graph Debugger](/assets/img/posts/open-graph-debugger.png "Open Graph Debugger")

Chờ chút xíu là xong. Thử chia sẻ lại link web bạn trên Facebook nhé. &copy;