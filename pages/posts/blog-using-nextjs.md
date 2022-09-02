---
title: Next.js 사용해서 블로그 만들기
date: 2022/9/2
description: Next.js와 템플릿을 사용해서 블로그를 만들었습니다.
tag: web development
---

# Next.js를 통해 공부한 것
우선 한 블로거가 [Next.js 튜토리얼을 한국어로 번역해 올린 글](https://brunch.co.kr/@hee072794/81)을 바탕으로 실행해 보았다. 
중간에 [데이터 가져오기](https://brunch.co.kr/@hee072794/88)에서 배트맨 쇼 데이터 가져오기 부분에서 에러가 발생했다. 
이 부분은 [또 다른 블로거의 글로그 글](https://alledy.netlify.app/posts/nextjs-basic/)을 참조했다.  
pages/index.js에서 아래와 같이 수정하니 배트맨 리스트가 잘 표시되었다. 

```
import Layout from '../components/MyLayout.js'
import Link from 'next/link'
import fetch from 'isomorphic-unfetch'

const Index = props => (
  <Layout>
    <h1>Batman TV Shows</h1>
    <ul>
      {props.shows.map(show => (
        <li key={show.id}>
          <Link href={`/post?id=${show.id}`} as={`/p/${show.id}`}>
            <a>{show.name}</a>
          </Link>
        </li>
      ))}
    </ul>
  </Layout>
)

Index.getInitialProps = async function() {
  console.log('init prop function is called')
  const res = await fetch('https://api.tvmaze.com/search/shows?q=batman')
  const data = await res.json()

  console.log(`show data fetched. count: ${data.length}`)
  return {
    shows: data.map(entry => entry.show),
  }
}

export default Index
```

이후 pages/post.js 부분도 첫 블로그 글대로 실행하면 에러가 발생했다. 
해당 페이지 역시 두 번째 블로그 글을 토대로 코드를 일부 수정했다. 

```
import Layout from '../components/MyLayout.js'
import fetch from 'isomorphic-unfetch'

const Post = props => {
    return (
      <Layout>
        <h1>{props.show.name}</h1>
        <p>{props.show.summary.replace(/<[/]?p>/g, '')}</p>
        <img src={props.show.image.medium}/>
      </Layout>
    )
  }
  
  export default Post
  
  Post.getInitialProps = async function(context) {
    const { id } = context.query
    const res = await fetch(`https://api.tvmaze.com/shows/${id}`)
    const show = await res.json()
  
    console.log(`Fetched show: ${show.name}`)
  
    return { show }
  }
```
