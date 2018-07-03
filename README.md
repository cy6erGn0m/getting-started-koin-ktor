---
layout: docs
title: Getting started with Ktor
description: Let's start with a Ktor web application & Koin
group: getting-started
toc: true
---

## About

Ktor is a framework for building asynchronous servers and clients in connected systems using the powerful Kotlin programming language. We will use Ktor here, to build a simple web application.

Let's go with the following components to chain : a Controller, a Service and a Repository.

{% highlight kotlin %}
Ktor Controller (http) -> Service (business) -> Repository (data)
{% endhighlight %}

- a Ktor Controller (routing function) to handle http route and return result from the service
- a Service to *handle business* and take data from repository
- a Repository to provide data

All sources are available in the Koin's [github repository](https://github.com/Ekito/koin-samples/tree/master/samples/ktor-hellowebapp)

Let's go 🚀

## Github & Sources

<div class="alert alert-secondary" role="alert">
    You can checkout the project directly from <a href="https://github.com/InsertKoinIO/getting-started-koin-ktor">Github</a> or download the <a href="https://github.com/InsertKoinIO/getting-started-koin-ktor/archive/master.zip">zip</a>
</div>

## Gradle Setup

First, add the Koin dependency like below:

{% highlight kotlin %}
// Add Jcenter to your repositories if needed
repositories {
    jcenter()    
}
dependencies {
    // Koin for Kotlin apps
    compile 'org.koin:koin-ktor:{{ site.current_version }}'
}
{% endhighlight %}

## Service & Repository

Let's write our Service, a component that will ask Repository for data:

{% highlight kotlin %}
interface HelloService {
    fun sayHello(): String
}

class HelloServiceImpl(val helloRepository: HelloRepository) : HelloService {
    override fun sayHello() = "Hello ${helloRepository.getHello()} !"
}
{% endhighlight %}

and our Repository, which provide data:

{% highlight kotlin %}
class HelloRepository {
    override fun getHello(): String = "Ktor & Koin"
}
{% endhighlight %}

## HTTP Controller

Finally, we need an HTTP Controller to create the HTTP Route. In Ktor is will be expressed through an Ktor extension function:

{% highlight kotlin %}
fun Application.main() {
    // Install Ktor features
    install(DefaultHeaders)
    install(CallLogging)

    // Lazy inject HelloService
    val service: HelloService by inject()

    // Routing section
    routing {
        get("/hello") {
            call.respondText(service.sayHello())
        }
    }
}
{% endhighlight %}

Check that your `application.conf` is configured like below, to help start the `Application.main` function:

{% highlight kotlin %}
ktor {
    deployment {
        port = 8080

        // For dev purpose
        //autoreload = true
        //watch = [org.koin.sample]
    }

    application {
        modules = [ org.koin.sample.HelloApplicationKt.main ]
    }
}
{% endhighlight %}

## Declare your dependencies

Let's assemble our components with a Koin module:

{% highlight kotlin %}
val helloAppModule = module {
    single { HelloServiceImpl(get()) as HelloService } // get() Will resolve HelloRepository
    single { HelloRepository() }
}
{% endhighlight %}

## Start and Inject

Finally, let's start Koin and Ktor:

{% highlight kotlin %}
fun main(args: Array<String>) {
    // Start Koin
    startKoin(listOf(helloAppModule))
    // Start Ktor
    embeddedServer(Netty, commandLineEnvironment(args)).start()
}
{% endhighlight %}

That's it! You're ready to go. Chech the `http://localhost:8080/hello` url!

## What's Next?

You have finished this starting tutorial. Below are some topics for further reading:

* Check the [DSL quick references]({{ site.baseurl }}/docs/{{ site.docs_version }}/quick-references/koin-dsl/)
* Check the [Core features quick references]({{ site.baseurl }}/docs/{{ site.docs_version }}/quick-references/koin-core/)
* Read the [full documentation]({{ site.baseurl }}/docs/{{ site.docs_version }}/documentation/reference/index.html)
