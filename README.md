# Presentation
Presentation
HISTORY (Slide1)
Next.js is a JavaScript framework that uses React to build Server Side Render (SSR) applications and statically generated websites. The Next.js framework was created relatively recently - in 2016 within Vercel. Its main task is to work with Server Side Render applications written in React. You can do it yourself using ReactDOMServer and conditional Express.js, but still this is not the best way, because the developer writes a lot of boilerplate code anyway. Next.js takes the development of SSR applications to the next level and add various optimizations. In general, you can write SSR applications yourself, just Next.js is a more optimized framework for such tasks (Static Generation and so on). 
 
NEXT.JS SIX CORE PRINCIPLES: (Slide2)
1. Work without setting. Using the File System as an API 
2. JavaScript only. Everything is a function 
3. Automatic Server Side Rendering and code-splitting 
4. The mechanism for obtaining data is determined by the developer 
5. Preload for better performance 
6. Easy deployment. 
 
HOW NEXT.JS WORKS (Slide 3)
Next.js is a framework that uses the React library. Its main difference from the “pure” React is in the way the final web pages are rendered. If React loads minimal HTML and often a large JS bundle (sometimes divided into modules between pages), then Next.js uses Server Side Rendering - the formation of the initial HTML on the server side using the same React. In the process, it optimizes the bundles so that it does not immediately send more code to the client side than is really necessary for a particular page, but simply load it later.
Let's compare step by step the process that happens after a page request using Next.js and React. 

IN NEXT.JS: (Slide 5)
import Head from 'next/head' 
import getProductData from "..."; 
 
export async function getServerSideProps(context) { 
  const product = getProductData(context.params.id); 
  return { 
    props: { 
      product 
    }, 
  }; 
} 
export default function Product({ product }) { 
  return ( 
    <div> 
      <Head> 
        <title>Купить {product.name}</title> 
      </Head> 
      <main> 
        <img src={product.image} alt="Product image" /> 
        <h1>{product.name}</h1> 
        <p>{product.description}</p> 
      </main> 
    </div> 
  ) 
}
   

1.	The browser makes a request to a product information page, for example, /product/1. 
2.	The server receives a request, downloads the necessary product data from another server, generates HTML based on the received data and the currently required React components. 
3.	The browser immediately receives HTML with the necessary information and shows it to the user, but the JS for interactivity has not yet been loaded. 
4.	JS is loaded after that in the background, after which it is embedded in the already existing HTML code - this process is called hydration. 
 
 
IN REACT(slide 6)

import { Helmet } from "react-helmet"; 
import getProductData from "..."; 
import { useParams } from "react-router-dom"; 
import { useEffect, useState } from "react"; 
 
const Product = () => { 
  const { productId } = useParams(); 
  const [product, setProduct] = useState(null); 
  useEffect(() => { 
    getProductData(productId) 
      .then(product => setProduct(product)); 
  }, [productId]); 
 
  if (!product) { 
    return <div>Загрузка</div> 
  } 
  return ( 
    <div> 
      <Helmet> 
        <title>Купить {product.name}</title> 
      </Helmet> 
      <main> 
        <img src={product.image} alt="Product image" /> 
        <h1>{product.name}</h1> 
        <p>{product.description}</p> 
      </main> 
    </div> 
  ) 
} 
 
export default Product; 

React:  
1.	The browser makes a request to a product information page, for example, /product/1. 
2.	The server returns a minimal HTML file that specifies the import of a JS file using React. 
3.	Once the HTML is loaded, the JS is loaded. 
4.	Only after the JS file is loaded, the necessary DOM elements are created, data is loaded from the server, and useful content is displayed. 


Comparing both processes, you can immediately notice the key differences between Next.js and React: 
 



PROS AND CONS OF NEXT.JS(Slide 7)

Pros of Next.js: 
1.	 When using Next.js, the browser immediately receives the finished HTML with the necessary information, without waiting for the JS to load. 
2.	Next.js makes requests to external APIs on the server side. 
Cons of Next.js: 
1.	 Next.js loads the JS after the HTML, until then the page will remain non-interactive. But even so, Next.js will load the minimum required JS first, and then the rest. 
2.	More server resources are needed because Next.js renders the HTML on the server side before sending it to the client. 
 
 
NEXT.JS ARCHITECTURE (Slide 8)
Understanding Next.js architecture 
You can create a new application on Next.js using a ready-made toolkit: 
 npx create-next-app my-app 
npx allows you to run executable npm packages without having to install them first. More details in the documentation. 
This command will create a new app in my-app folder and install all necessary dependencies:  
Next.js supports eslint and CSS modules out of the box (in the styles folder). Static files can be placed in the public folder, just like in regular React. But the most important folder for us is pages. 
 
HOW NEXT.JS GENERATES ACCESSIBLE URLS AND OPTIMIZES PAGES(Slide 9). 
 
Each file inside this folder (except for the api folder, _app and _document files) is treated by Next.js as a separate page. It is important to remember this (why - we will tell a little further). This is how Next.js automatically converts the file structure to a URL, including dynamic parameters. It will become clearer if you look at an example: 
  
Now let's look at an example users/[id]/index.js file: 

import Head from 'next/head' 
import getUserData from "..."; 
export async function getServerSideProps(context) { 
    const user = getUserData(context.params.id); 
    return { 
        props: { 
            user: user 
        }, 
    }; 
} 
 
export default function User({user}) { 
    return ( 
        <div> 
            <Head> 
                <title>Страница {user.name}</title> 
            </Head> 
            <main> 
                <img src={user.avatar} alt="User photo"/> 
                <h1>{user.name}</h1> 
                <p>{user.bio}</p> 
            </main> 
        </div> 
    ) 
} 
Here we see the default export of the main User component, which displays information about the user. It takes this information from props, but how does it get there? 
Here Next.js gives us two options that lead to different results. Both options work as named exports: 
getServerSideProps will be called on every request while the application is running. Inside, you can make calls to external services and APIs to get the latest up-to-date information. Depending on whether a person is visiting the site for the first time or simply moving between pages, this call will be made either on the server or on the client. 
getStaticProps will be called once when building the application, it will prepare the finished HTML files. Let's say you have a list of articles that are rarely updated. They can be pre-rendered with getStaticProps and these pages will load faster since they already have all the information. 
Both functions need to be exported with this named export so that Next.js can see and use them. 
  
Footnote: if the page does not have dynamic content, it does not load data from the server and just contains static content, then Next.js will optimize it and prepare a ready-made HTML file that will simply be given to the client. Because of this, components cannot be stored directly in the pages directory. Next.js will treat them as separate pages and try to optimize them, resulting in longer build times. Therefore, pages do not store anything other than simple components that import content from somewhere else in another folder. 
  
You can read more about ways to optimize pages and how to use them correctly in the documentation. 
 
API directory(Slide 10)
This is one of the newest Next.js features. It allows you to describe methods that will only run on the server. Roughly speaking, this is a “layer” between the frontend and external services, which can be easily implemented right in the project. 

const users = { 
    "1": { name: "Билл Гейтс", bio: "Создатель Windows", avatar: "https://..." }, 
    "2": { name: "Стив Джобс", bio: "Основатель Apple", avatar: "https://..." }, 
    "3": { name: "Джон Кармак", bio: "Разработчик Doom", avatar: "https://..." }, 
} 
 
export default function handler(req, res) { 
    const { id } = req.query; 
    res.status(200).json(users[id]); 
} 

The default export of a function is expected, which will process requests and return responses. Anything can be inside it, in our example, this is getting the user by the id parameter that we specified in the URL. 
  
Since this method only runs on the server, we can safely access external services and environment variables without exposing extra information to the client. Roughly speaking, this is just a layer between the frontend and various APIs, you should not do a full-fledged backend here. 
 
A little about _app and _document(Slide 11)
_app and _document are used to customize all pages at the same time, but serve different functions. 
 _app is needed to build common logic between all pages. For example, Redux, authentication services, various caches, and so on can be connected here. If you use getServerSideProps or getInitialProps (its older deprecated brother) inside _app, then all your pages are processed at runtime - and this disables static optimization (and NextJS will hasten to inform you about this).  _document is needed to edit the HTML template that is used when rendering all pages. Most of the time you won't need to add or change it, but it is needed to include css-in-js or render various static html blocks that will always be in the page template. 
 
Additionally(Slide 12)
Next.js provides features and components that are needed for specific use cases. Brief description of the most commonly used: 
 next/router is a module for working with the Next.js router. Needed for programmatic redirects, getting data from a URL on the client. 
 next/link is a Link component that renders the "correct" links. In HTML markup, they will be regular <a> tags and will work even if the user has JS disabled. If JS is enabled, a transition will occur on click using client-side rendering and Next.js optimizations. 
 next/head is a Head component that allows you to change the content of the <head> tag without regard to the server/client render. 
 next/script - Script component for connecting external scripts on the client (for example, metrics). 
 
Development and future of the framework(Slide 13)
A large and active community has formed around Next.js, and the framework itself is actively supported and developed by developers. All updates are published on the blog and are detailed in the documentation, next to a full-fledged interactive tutorial). Future updates will include React 18 adaptations, new file conventions, parallel data queries, and more. 
  
Next.js is actively used and supported by large companies like Google, and Vercel offers a convenient infrastructure for deploying applications. So it's safe to say that Next.js will not die in the near future, and you can safely use it for production, as many other companies do. 
