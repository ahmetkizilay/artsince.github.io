---
layout: post
title: Integration testing with dockerized Selenium and Capybara
tags: docker, capybara, selenium, testing
excerpt: Setting up integration testing with docker-compose.
---

### {{page.title}}

In most cloud-based CI providers such as Travis or Codeship, there is support for Firefox and Chrome browsers out of the box. Surely this is very convenient as Karma or Capybara runners will work painlessly during testing.

My Continuous Integration workflow, however, lives entirely in Docker, so my test runner does not have access to the browser on the host system. One solution is to use PhantomJS, but I kept bumping into some unsupported operation errors with it for some third-party Javascript libraries. So, I believe working with Selenium is less painful and more reliable.

Below I demonstrate how to setup and configured my Rails app to run integration tests on a windowless browser using docker-compose.

To begin, Capybara works with default Firefox installation with no extra effort at all. Here is a sample integration test:
<script src="https://gist.github.com/artsince/d6c3b42fa4b741a81d1c.js?file=one.rb"></script>

With docker-compose, I will create two containers, one for my Rails app, and one for the Selenium Firefox browser. These two containers will run on the same Docker network and they will be able to communicate with each other over this private bridge network.

Here is the docker-compose.yml file:
<script src="https://gist.github.com/artsince/d6c3b42fa4b741a81d1c.js?file=two.yml"></script>

I will simply test with the Firefox browser, so I do not need to set up a Selenium grid. Stand-alone Firefox image will be enough.

Now, I need to configure and register the Capybara driver in my `rails_helper.rb` file. Since the Selenium container will be on a different IP than my app, the driver needs to be configured for remote.

<script src="https://gist.github.com/artsince/d6c3b42fa4b741a81d1c.js?file=three.rb"></script>

`setup_driver` is a convenience method I will use before my tests to set the remote Selenium browser. So, I add it to the start of my test.

<script src="https://gist.github.com/artsince/d6c3b42fa4b741a81d1c.js?file=four.rb"></script>

Thanks to the Docker bridge networking, both containers will be assigned an IP and a host name, and they will have access to each other over the network. I pass the addresses of the containers as environment variables in the `start-tests.sh` file. For convenience, the container_name is set as the host name in the network, so I can simply use it in the script file.

<script src="https://gist.github.com/artsince/d6c3b42fa4b741a81d1c.js?file=five.sh"></script>

Note that, since networking feature was experimental up until version 1.6, you need to pass `--x-networking` flag for earlier versions of docker-compose.
<br/>
<br/>
With this setup, I can seamlessly run my unit as well as integration tests inside my Docker workflow with Gitlab runner on my private server. I hope you also find it useful. For any questions or comments, feel free to get in touch by writing a comment below or sending me a tweet  [@ahmetkizilay](https://twitter.com/ahmetkizilay). Enjoy!
