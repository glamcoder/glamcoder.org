---
title: Azure Websites Testing in Production to win
author: Georgiy Mogelashvili
type: post
date: 2014-12-14T09:32:51+00:00
url: /azure-websites-testing-in-production-to-win-en/
interface_sidebarlayout:
  - default
categories:
  - Microsoft Azure
tags:
  - A/B testing
  - Azure Websites
  - split testing
  - Testing in Production

---
_*This service has been recently renamed to Traffic Routing. Don't be confused, article still applies to it‚_

Hi!

I&#8217;d like to tell you about very new service in the Microsoft Azure family called Azure Websites Testing in Production (or just TiP). It hasÂ not been publicly announced yet but it&#8217;sÂ already available toÂ play with. This service will help you manage and test your website deployments more reliably. Also you can implementÂ A/BÂ testing with it. So before we start I&#8217;d like to say some words about what it is and why you might need it.

# A/B testing

If we think about the name itself then we can suggest that <a title="A/B testing" href="http://en.wikipedia.org/wiki/A/B_testing" target="_blank">A/B testing</a> is about testing A against of B. And that&#8217;s 100% correct. This is also called split-testing or testing in production. It helps you analyze users&#8217; behavior based on some variable parameters or features. Imagine you want to implement something completely new for your website. Let&#8217;s say &#8212; change background color from red to blue. How would you then know that people like that? What ifÂ new color will annoy your visitors and they will convert less (by conversion you may consider everything &#8212; payment, ads click, subscription etc)? To prevent yourself from assumptions based on your only feelings (which unfortunately might be wrong) you can run some A/B tests against your hypothesis. This means thatÂ you have two deployments running in parallel: one of them has old design and functionality (base) and the second has new features deployed (variant). Then if you split your incoming traffic 50/50Â and monitor users&#8217; behavior you will be able toÂ make you decisions based on statistics and real data. IfÂ variant converts better than make it your new base and direct 100% traffic there. But if not then keep base and think about new features to implement.<!--more-->

Sounds simple, isn&#8217;t it? In fact A/B testing is much more complicated. There is lots of statistics there. You can&#8217;t just take A, compare it to B and be 100% sure in results. You have to consider every errors in numbers you might have just because your users can behave in tons of different ways depending on day, time, weather etc.Â As an example imagine, that you haveÂ an online store which sells toys.Â It&#8217;s Christmas time now and you decide to paint your website into red and green and put Santa Claus next to the logo. You make it as an A/B test and run it from 20th till 25th of December.Â After that time you may have positive results for your variant (the one with Santa). But what happens if you make your decisionÂ based on that data? You put your variant as a base forever from now and will be very surprised that you start earning less money in January, February etc. That happened because you weren&#8217;tÂ running your test for a long enough time to have your results conclusive. Of course this example is very naive butÂ gives you a picture of what may happen if youÂ ignore statistics.

You can read more about A/B testingÂ <a href="https://www.google.com/search?q=a%2Fb%2F+testing" target="_blank">somewhere in the Internet</a>Â and now let&#8217;s talk about how you can run your own A/B tests with Microsoft Azure.

# Tools

Let&#8217;s think that you are now good at theory behind A/B testing and know how to <del>manipulate</del> use statistics when making decisions. But another problem we have now is how to implement this technique?Â How should your website understand which variant should be exposed to this or that visitor? And who shall dispatch users &#8212; application itself or low level web server? There are more questions there.Â What should we do with new visitor? Website have to divide users between buckets (base and variant) randomly but evenly. Every returning visitor we can dispatch as a newcomer or put him in the bucket he was put before.Â If you decide to implement A/B testing by yourself there a lots of thing to do.

Luckily there are some tools which provide A/B out of the box. For example, <a title="MailChimp" href="http://mailchimp.com/features/ab-testing/" target="_blank">MailChimp</a>Â can send two (or more) different versions of emails to your customers so that you can choose one which performs better. There are some tools for website testing as well, for example <a title="Optimizely" href="https://www.optimizely.com/" target="_blank">Optimizely</a>,Â <a title="Visual Website Optimizer" href="https://vwo.com/" target="_blank">Visual Website Optimizer</a>Â and <a title="Google Analytics" href="https://support.google.com/analytics/answer/1745147" target="_blank">Google Analytics</a>. ButÂ they only allow you to test content changes, not functionality of your website (new features, performance improvements etc). All those tools are for marketing purposes only and not suitable for technical stuff. I hope that you can point me out betterÂ tools for A/B testing if any in the comments. From my side I will tell you about how Microsoft Azure can handle all theÂ infrastructure part for you.

# Azure Websites Testing in Production

As I said earlier, Azure Websites Testing in ProductionÂ has not been publicly announced yet, but you already have access to all it&#8217;s power in new <a title="Azure Portal" href="https://portal.azure.com/" target="_blank">Azure Portal</a> (aka Ibiza).

In fact TiP is more than just A/B testing.Â You can use it as a complex solution for validation your business ideas and deployments. There are several scenariosÂ of usage:

  * Canary Testing &#8212; test your new, probably unstable, feature on a small amount of your loyal visitors by providing them a link to the updated version of your website.
  * Ramp-Up testing (aka Canary Deployment) &#8212; test your prototype by slowly increasing number of visitors seeing new feature. You can start with 5%,Â monitor performance/errors for a while and add another 5%. Do this until you find some critical errors orÂ until you reach 100% (then you&#8217;ll have your new base).
  * Recovery Testing &#8212;Â having stable slot operating while introducing something new will let you fallback toÂ working solution very fast in case of unexpected errors. You mayÂ store more than one deployments of a product and switch them if necessary.
  * A/B Testing &#8212; the one I was talking about. Consider it as a generic scenario, which includes previous three.

Hopefully, you don&#8217;t need to read tons of documentation in order to implement all those scenarios.Â You can do this in one place in AzureÂ by tuning some parameters. Below I&#8217;ll finally explain what to do.

# Prepare

Well, obviously, in order to be able to use Azure Websites Testing in Production your web application should be running in Azure. More of that it has to be set up as Standard pricing tier (you need Slots be available and they are only in Standard).

<div id="attachment_3136" style="width: 1283px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/01_CreateWebsite.png"><img class="wp-image-3136 size-full" src="https://glamcoder.ru/wp-content/uploads/2014/10/01_CreateWebsite.png" alt="Create Website For Azure Websites Testing In Production" width="1273" height="749" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/01_CreateWebsite.png 1273w, https://glamcoder.ru/wp-content/uploads/2014/10/01_CreateWebsite-300x176.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/01_CreateWebsite-1024x602.png 1024w" sizes="(max-width: 1273px) 100vw, 1273px" /></a>
  
  <p class="wp-caption-text">
    Choose which website you will be testing
  </p>
</div>

Open website blade and go to its bottom. There you will find &#8216;Testing in production&#8217; tile:

<div id="attachment_3138" style="width: 597px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/01_TiPPart.png"><img class="size-full wp-image-3138" src="https://glamcoder.ru/wp-content/uploads/2014/10/01_TiPPart.png" alt="Azure Websites Testing in Production Part" width="587" height="749" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/01_TiPPart.png 587w, https://glamcoder.ru/wp-content/uploads/2014/10/01_TiPPart-235x300.png 235w" sizes="(max-width: 587px) 100vw, 587px" /></a>
  
  <p class="wp-caption-text">
    Create new Testing in Production
  </p>
</div>

<div id="attachment_3139" style="width: 1192px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/03_AddSlot.png"><img class="size-full wp-image-3139" src="https://glamcoder.ru/wp-content/uploads/2014/10/03_AddSlot.png" alt="Add Azure Websites Testing in Production Slot" width="1182" height="749" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/03_AddSlot.png 1182w, https://glamcoder.ru/wp-content/uploads/2014/10/03_AddSlot-300x190.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/03_AddSlot-1024x648.png 1024w" sizes="(max-width: 1182px) 100vw, 1182px" /></a>
  
  <p class="wp-caption-text">
    Create testing in production
  </p>
</div>

You will be asked to create a new deployment slot.Â Feel free to choose asÂ many slots as you need for testing purposes. By the way, you might have been familiar with deployment slots already. They were introducedÂ <a href="http://weblogs.asp.net/scottgu/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance" target="_blank">a year ago</a>Â as a cool feature for staging your deployments. Read more in <a title="Staged Deployment on Microsoft Azure Websites" href="http://azure.microsoft.com/en-us/documentation/articles/web-sites-staged-publishing/" target="_blank">this documentation article</a>. So, this exactly feature is used for testing in production purposes.

To create a new slot you only have to specify its name and configuration source if you want to re-use some config details from your running website. Please keep in mind that &#8216;_production_&#8216; name is system reserved for main application version so please don&#8217;t be surprised that you can&#8217;t select it.

<div id="attachment_3140" style="width: 1283px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/04_AddSlot_2.png"><img class="wp-image-3140 size-full" src="https://glamcoder.ru/wp-content/uploads/2014/10/04_AddSlot_2.png" alt="Add new slot for Azure Websites Testing in Production" width="1273" height="749" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/04_AddSlot_2.png 1273w, https://glamcoder.ru/wp-content/uploads/2014/10/04_AddSlot_2-300x176.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/04_AddSlot_2-1024x602.png 1024w" sizes="(max-width: 1273px) 100vw, 1273px" /></a>
  
  <p class="wp-caption-text">
    Add new slot for testing
  </p>
</div>

Let&#8217;s create three new slots and give them some sensible names:

<div id="attachment_3142" style="width: 1278px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/05_SlotsList.png"><img class="size-full wp-image-3142" src="https://glamcoder.ru/wp-content/uploads/2014/10/05_SlotsList.png" alt="Azure Websites Testing in Production Slots" width="1268" height="751" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/05_SlotsList.png 1268w, https://glamcoder.ru/wp-content/uploads/2014/10/05_SlotsList-300x177.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/05_SlotsList-1024x606.png 1024w" sizes="(max-width: 1268px) 100vw, 1268px" /></a>
  
  <p class="wp-caption-text">
    Slots for testing in production
  </p>
</div>

Each slot is by fact a standalone website with its own files and config and can be easily accessed by a URL like _<main\_website\_name>-<slot_name>.azurewebsites.net_. You can deploy your code to a slot with Publish Profile or FTP or whatever you do for regular website deployment.

After you set up and configure all your slots you are ready for testing.

# Set up testing

In order to beginÂ testing (A/B or Canary or whatever) you have to specifyÂ percentage of your visitors to be routed into each slot you have. Go back to Testing in production blade.Â When slots are created you will be able to adjust traffic for each of them:

<div id="attachment_3143" style="width: 1281px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/06_AddTests.png"><img class="size-full wp-image-3143" src="https://glamcoder.ru/wp-content/uploads/2014/10/06_AddTests.png" alt="Azure Websites Testing in Production slots configuration" width="1271" height="748" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/06_AddTests.png 1271w, https://glamcoder.ru/wp-content/uploads/2014/10/06_AddTests-300x176.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/06_AddTests-1024x602.png 1024w" sizes="(max-width: 1271px) 100vw, 1271px" /></a>
  
  <p class="wp-caption-text">
    Configure slots in Azure Websites Testing in Production
  </p>
</div>

While editing your slots you always will see how many of your visitors will be routed toÂ your base. It&#8217;s obvious when you have only one-two variants but can be handy if you have dozens of them (good luck inÂ monitoring all these).

By the way, as each slot is a regular website, you can then create more complex scenarios with testing inside testing (did you watch Inception movie?).

<div id="attachment_3145" style="width: 1279px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/07_TreeTests.png"><img class="size-full wp-image-3145" src="https://glamcoder.ru/wp-content/uploads/2014/10/07_TreeTests.png" alt="Complex tests in Azure Websites Testing in Production" width="1269" height="748" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/07_TreeTests.png 1269w, https://glamcoder.ru/wp-content/uploads/2014/10/07_TreeTests-300x176.png 300w, https://glamcoder.ru/wp-content/uploads/2014/10/07_TreeTests-1024x603.png 1024w" sizes="(max-width: 1269px) 100vw, 1269px" /></a>
  
  <p class="wp-caption-text">
    Complex tests inside tests
  </p>
</div>

Please note the name of a website on a previous picture. It&#8217;s no more a _glamcoder_, it&#8217;s _glamcoder-tip-test-1_, which is a testing slot with 10% of traffic (set few steps before). But now inside the testing slot we have one more slot (test-1) which gets 50% of traffic. So, we have 50% out of 10%, which results into 5%Â of all traffic.

Why do you need this? Well, ifÂ you are OKÂ with exposing new feature to 50% of your visitors, then youÂ can just have one level of slots. You will then get a normal distribution and even amount of visitors in each deployment, which allowsÂ you make you decision statistically correct. But what if you want only 10% of your visitors to see the changes? You can&#8217;t compare 10% to 90% then because it&#8217;s obviously can&#8217;t be correct. That&#8217;s whereÂ second level of testing slots might help. If you have a slot withÂ variant, and then inside this slot you again split your visitors to variant and base, then you can compare them between each other as if it&#8217;s a regular 50/50 distribution. You only need to measure and track them correctly.

# How it works

There is no information about how it works in particular. But what I can tell you is that you as a developer don&#8217;t have to do anything specific in order to make it work. All the distribution is made under the hood and completely obscure to your visitor. He just types aÂ URL of your website as always but then somewhere inside Azure Websites he will landÂ onÂ the corresponding slot automatically. The decision isÂ based on some statistical algorithms but as far as IÂ know you don&#8217;t have to worry about that and be sure that each slot will get only that amount of visitors it was configured for.

On theÂ very first visitÂ a cookie will be created for a user. It will have a slot ID as a value.Â Whenever this user returns to your website he will be automatically routed to the same slot he was before. That means that user&#8217;s experience will remain the sameÂ for all the time you run an experiment.

<div id="attachment_3150" style="width: 614px" class="wp-caption aligncenter">
  <a href="https://glamcoder.ru/wp-content/uploads/2014/10/08_Cookie.png"><img class="size-full wp-image-3150" src="https://glamcoder.ru/wp-content/uploads/2014/10/08_Cookie.png" alt="Azure Websites Testing in Production Cookie" width="604" height="269" srcset="https://glamcoder.ru/wp-content/uploads/2014/10/08_Cookie.png 604w, https://glamcoder.ru/wp-content/uploads/2014/10/08_Cookie-300x133.png 300w" sizes="(max-width: 604px) 100vw, 604px" /></a>
  
  <p class="wp-caption-text">
    Cookie with testing in production slot ID
  </p>
</div>

# Monitoring

For now there is no out-of-the-box built-in solutions for monitoring yourÂ Azure Websites Testing in Production. You can pick whatever you like most. You can use <a title="Azure Websites Analytics" href="http://channel9.msdn.com/Shows/Azure-Friday/Adding-Analytics-to-Azure-Web-Sites" target="_blank">Azure Analytics</a>,Â Google Analytics or any other service you like. It&#8217;s only you who knows how to track conversion rate and what is good for your site. I hope that Microsoft will bring some advanced tools for monitoring A/B tests but for now you can create your own (which might be better or not).

# What&#8217;s next

You can ask now &#8212; what am I supposed do with this new and unfinished service? Well, that&#8217;s quite a reasonable question. I wrote this article wittingly before this service is publiclyÂ announced because. I wanted to tell you about great opportunity for each website owner to become more confident in decision making. You don&#8217;t have to be blind now when releasing new feature. Please, test it on small amount of your visitors first and then release it to everyone or to none. I truly believe that every websiteÂ should do that because only your users can tell you what theyÂ like and what they hate.Â So let them test your website while you stayingÂ aside and monitoring what&#8217;s going on.

There is also a great introductionary video about Azure Websites Testing in Production on <a title="Azure Websites Testing in Production" href="http://channel9.msdn.com/Shows/Web+Camps+TV/Enabling-Testing-in-Production-in-Azure-Websites" target="_blank">Channel 9</a>.

# P.S.

If you decided toÂ start usingÂ Azure Websites Testing in Production for your website and faced someÂ issues, bugs or have any otherÂ feedback please contact me. I will forward your message to a developmentÂ team so that they can make the product even better.