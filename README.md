# skaws2003.github.io

> 템플릿 출처: 카카오 테크 블로그 (https://github.com/kakao/kakao.github.io)  
> 수정: skaws2003 (https://github.com/skaws2003)  
> 라이센스: Apache 2.0


### 수정 내역
전체적인 레이아웃을 개인 블로그에 맞게 수정했습니다.
* 저작권 문제의 있는 리소스를 삭제
  * 카카오 폰트 전부 삭제
  * 카카오 로고 및 기타 이미지 리소스 전부 삭제
  * 삭제된 리소스를 상업용 무료 리소스(혹은 자작 리소스)로 대체
* 상단 네비게이터를 개인 블로그에 맞게 정리 
  * "오픈소스", "오픈API", "기술행사" 메뉴 삭제
  * "Resume" 버튼을 추가하여 이력서로 이동할 수 있도록 정리
  * 삭제된 메뉴의 리소스 정리
* 최하단의 각종 링크를 개인 블로그에 맞게 정리
  * "Kakao", "Jobs", "Pravacy" 링크를 "Resume", "Github", "Source"로 대체
* 페이지 중앙의 광고 칸 삭제
* 기타 등등


### 설치

1. <https://github.com/skaws2003/skaws2003.github.io>를 포크, 레포 이름을 개인 용도에 맞게 변경
2. 포크된 레포지토리를 클론
3. `bundle install`로 필요한 것들을 설치

```console
$ git clone git@github.com:YOUR_GITHUB_ACCOUNT/YOUR_REPOSITORY_NAME
$ cd YOUR_REPOSITORY_NAME
$ git checkout master
$ bundle install
```


### 실행(로컬)

```
$ bundle exec jekyll serve
$ open http://localhost:4000
```


### 글 쓰기

1. 처음 글을 쓰는 작성자면 **글쓴이 등록**
2. 새로운 태그를 사용할 거면 **태그 등록**
3. `_posts` 디렉토리에 `yyyy-mm-dd-slug.md` 파일로 복사(or 이동).
 - slug: 해당 포스트의 고유 키로 url의 일부로 사용. 왠만하면 특수문자없이 영문자,숫자,-(하이픈),.(점)...만 사용.
 - yyyy,mm,dd: 발행 년,월,일.
 - 참고: 최종적으로 포스트의 url(permalink)는 http://skaws2003.github.io/yyyy/mm/dd/slug/
4. 파일 상단에 [front matter] 작성
 - layout: 레이아웃. 디폴트는 `post`. `page` 레이아웃을 사용하면 목록에 보이지 않는 글을 쓸 수 있음
 - title: 제목
 - author: 글 작성자
 - tags: `[tag1,tag2,tag3,...]` # 태그 목록. 웬만하면 특수문자없이 영소문자,숫자,-(하이픈),.(점)...만 사용.
 - image: `http://...` # 커버이미지 url
 - date: `YYYY-MM-DD HH:MM:SS` # 작성 시각. 이것에 따라 포스트가 정렬됨.


### 글쓴이 등록

1. `_authors` 디렉토리에 `이름.md` 이름으로 필자 정보 파일 추가
  - 참고: 최종적으로 사용자 포스트 목록 페이지의 url은 http://skaws2003.github.io/authors/이름/
2. 파일 상단에 [front matter] 작성
 - layout: author # 레이아웃
 - name: 글쓴이 이름 # post의 author와 똑같게. 왠만하면 특수문자없이 영소문자,숫자,-(하이픈),.(점)...만 사용.
 - title: 글쓴이 페이지의 제목. 한글 이름을 권장
 - image: http://... # 작성자 프로필 이미지
 - cover: http://... # 작성자 커버 이미지
3. 내용은 필요없음


### 태그 등록

1. `_tags` 디렉토리에 `태그이름.md` 이름으로 태그 정보 파일 추가
 - 참고: 최종적으로 사용자 포스트 목록 페이지의 url은 http://skaws2003.github.io/tags/태그이름/
2. 파일 상단에 [front matter] 작성
 - layout: tag # 레이아웃
 - name: `태그이름` # post의 tags 항목과 같게. 왠만하면 특수문자없이 영소문자,숫자,-(하이픈),.(점)...만 사용.
 - title: ... # 좀 더 길고 구체적인 설명
3. 내용은 필요없음

---

May the **SOURCE** be with you...

[GitHub Pages]: https://pages.github.com
[Jekyll]: https://jekyllrb.com
[front matter]: https://jekyllrb.com/docs/frontmatter/
[gfm]: https://guides.github.com/features/mastering-markdown/
[kramdown]: http://kramdown.gettalong.org
[rouge]: http://rouge.jneen.net


## License

This software is licensed under the [Apache 2 license](LICENSE.txt), quoted below.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this project except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
