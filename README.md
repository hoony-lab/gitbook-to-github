# gitbook-to-github

## **깃북아, 이사가야지** 

* 왜 깃북과 깃헙은 바로바로 연동이 되지 않고 지금까지 수정하지 않았을까 😂
* 이미지도 이상해, 버젼 관리도 안돼,,,



### 깃헙 &gt; 깃북 연동시 파일 순서가 이상하다

* 처음부터 손봐야 할 것 같다



### 깃헙 &gt; 깃북 연동시 이미지 제대로 나오지 않는게 있다

다음은 깃북에서 선사하는 [두 가지 글 작성 방식](https://docs.gitbook.com/editing-content/rich-content)이다. 

> Thanks to our elegant _WYSIWYG_ editor, you can have **rich-content** and **rich-text**. ****✨ Let's start with rich content!
>
> There are 2 ways to add rich content to your docs:
>
> * With the [command palette](): choose among our rich content options and bring some life to your documentation.
> * With the [insert palette](): insert images, math formulas, links or emojis while writing your content.

 **rich-content** 와 **rich-text** 라고 하는데 ~~내가 부자였으면 좋겠다.~~ 두 가지를 살펴 보자.



#### 1. [command palette](https://docs.gitbook.com/editing-content/rich-content/with-command-palette)

> The most common type of images is an "image block". They are full-width images containing a caption. You can center or align them to the left. You can insert them like this:

**Image block** 이라는 이미지 타입은 자동으로 이미지 사이즈가 **크게** 나온다. 그리고 이미지 **왼쪽 정렬**과 **가운데 정렬**을 지원한다.

![](.gitbook/assets/assets_-ljqes59tx3tzs90rqcl_-lreeufd9zenr1rzuwov_-lref-32qcka04sxpcmd_image-block.gif)



#### 2. [insert palette](https://docs.gitbook.com/editing-content/rich-content/with-insert-palette)

> You can insert inline images to your content. By default, their size is proportional to the font size as their main purpose is to be inserted in line to your content. This is great for inline badges and icons.‌
>
> There are 3 different sizes of inline images:‌
>
> 1. **Inline size:** the default one proportionally sized to the font
> 2. **Original size:** will remain inline but with its original size with a maximum width
> 3. **Convert to block:** this turns an inline image into a [block image]() with its original size
>
>   
>  **Note:** You cannot convert a block image to an inline image.

3가지 이미지 사이즈 방식을 지원한다.

1. 폰트 사이즈와 동일한 이미지 사진
2. 이미지 본 사이즈
3. 위에서 본 block image 로 변

![](.gitbook/assets/assets_-ljqes59tx3tzs90rqcl_-lrezu7opjmjynkvzk9u_-lre_fbwrho8q93ttjmn_image-insert-palette.gif)



#### 3. 바

> 깃북에서는 소스보기가 지원 되지 않고 바로 md preview가 진행된다.

깃북 &gt; 깃헙 _\(깃북의 기능인 Integrations 를 통해 깃헙으로 연동\)_ 을 하고 소스를 보면 다음과 같다. command 방식과 insert 방식 둘 다 동일한 소스로 나온다.

```text
![](.gitbook/assets/5.png)
```

그리고 깃헙 &gt; 깃북을 진행하게 되면 어떻게나왔더라...?



깃북 &gt; 깃헙 연동을 하면 Image block 형식으로 저장이 된다. 아마 기본 형식인듯 하다.

깃헙 &gt; 깃북 연동을 하면 Image block 형식으로 저장이 된다.





### 깃북아, 버젼 관리는 어떻게 

### 1. [variants](https://docs.gitbook.com/editing-content/variants)

variants 진곰 ? 질려 ?  
  
‌

![](.gitbook/assets/assets_-ljqes59tx3tzs90rqcl_-lrewevvci5qm1ri5uw7_-lrewjhcvayei6gazhuq_variants.gif)

