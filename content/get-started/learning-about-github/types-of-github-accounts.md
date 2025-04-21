---
title: Types of GitHub accounts
intro: 'Accounts on {% data variables.product.github %} allow you to organize and control access to code.'
redirect_from:
  - /manage-multiple-clients
  - /managing-clients
  - /articles/what-s-the-difference-between-user-and-organization-accounts
  - /articles/differences-between-user-and-organization-accounts
  - /articles/types-of-github-accounts
  - /github/getting-started-with-github/types-of-github-accounts
  - /github/getting-started-with-github/learning-about-github/types-of-github-accounts
versions:
// Copyright 2019 The Chromium Authors
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.
import{assert,assertNotReached}from"//resources/js/assert.js";import{CrLitElement}from"//resources/lit/v3_0/lit.rollup.js";import{getCss}from"./cr_lottie.css.js";import{getHtml}from"./cr_lottie.html.js";let workerLoaderPolicy=null;function getLottieWorkerURL(){if(workerLoaderPolicy===null){workerLoaderPolicy=window.trustedTypes.createPolicy("lottie-worker-script-loader",{createScriptURL:_ignore=>{const script=`import 'chrome://resources/lottie/lottie_worker.min.js';`;const blob=new Blob([script],{type:"text/javascript"});return URL.createObjectURL(blob)},createHTML:()=>assertNotReached(),createScript:()=>assertNotReached()})}return workerLoaderPolicy.createScriptURL("")}export class CrLottieElement extends CrLitElement{constructor(){super(...arguments);this.animationUrl="";this.autoplay=false;this.hidden=false;this.singleLoop=false;this.canvasElement_=null;this.isAnimationLoaded_=false;this.offscreenCanvas_=null;this.hasTransferredCanvas_=false;this.resizeObserver_=null;this.playState_=false;this.workerNeedsSizeUpdate_=false;this.workerNeedsPlayControlUpdate_=false;this.worker_=null;this.xhr_=null}static get is(){return"cr-lottie"}static get styles(){return getCss()}render(){return getHtml.bind(this)()}static get properties(){return{animationUrl:{type:String},autoplay:{type:Boolean},hidden:{type:Boolean},singleLoop:{type:Boolean}}}connectedCallback(){super.connectedCallback();this.worker_=new Worker(getLottieWorkerURL(),{type:"module"});this.worker_.onmessage=this.onMessage_.bind(this);this.initialize_()}disconnectedCallback(){super.disconnectedCallback();if(this.resizeObserver_){this.resizeObserver_.disconnect()}if(this.worker_){this.worker_.terminate();this.worker_=null}if(this.xhr_){this.xhr_.abort();this.xhr_=null}}updated(changedProperties){super.updated(changedProperties);if(!changedProperties.has("animationUrl")){return}if(!this.worker_){return}if(this.xhr_){this.xhr_.abort();this.xhr_=null}if(this.isAnimationLoaded_){this.worker_.postMessage({control:{stop:true}});this.isAnimationLoaded_=false}this.sendXmlHttpRequest_(this.animationUrl,"json",this.initAnimation_.bind(this))}setPlay(shouldPlay){this.playState_=shouldPlay;if(this.isAnimationLoaded_){this.sendPlayControlInformationToWorker_()}else{this.workerNeedsPlayControlUpdate_=true}}sendPlayControlInformationToWorker_(){assert(this.worker_);this.worker_.postMessage({control:{play:this.playState_}})}initialize_(){this.canvasElement_=this.$.canvas;this.offscreenCanvas_=this.canvasElement_.transferControlToOffscreen();this.resizeObserver_=new ResizeObserver(this.onCanvasElementResized_.bind(this));this.resizeObserver_.observe(this.canvasElement_);if(this.isAnimationLoaded_){return}this.sendXmlHttpRequest_(this.animationUrl,"json",this.initAnimation_.bind(this))}getCanvasDrawBufferSize_(){const canvasElement=this.$.canvas;const devicePixelRatio=window.devicePixelRatio;const clientRect=canvasElement.getBoundingClientRect();const drawSize={width:clientRect.width*devicePixelRatio,height:clientRect.height*devicePixelRatio};return drawSize}isValidUrl_(maybeValidUrl){const url=new URL(maybeValidUrl,document.location.href);return url.protocol==="chrome:"||url.protocol==="data:"&&url.pathname.startsWith("application/json;")}sendXmlHttpRequest_(url,responseType,successCallback){assert(this.isValidUrl_(url),"Invalid scheme or data url used.");assert(!this.xhr_);this.xhr_=new XMLHttpRequest;this.xhr_.open("GET",url,true);this.xhr_.responseType=responseType;this.xhr_.send();this.xhr_.onreadystatechange=()=>{assert(this.xhr_);if(this.xhr_.readyState===4&&this.xhr_.status===200){const response=this.xhr_.response;this.xhr_=null;successCallback(response)}}}onCanvasElementResized_(){if(this.isAnimationLoaded_){this.sendCanvasSizeToWorker_()}else{this.workerNeedsSizeUpdate_=true}}sendCanvasSizeToWorker_(){assert(this.worker_);this.worker_.postMessage({drawSize:this.getCanvasDrawBufferSize_()})}initAnimation_(animationData){const message={animationData:animationData,drawSize:this.getCanvasDrawBufferSize_(),params:{loop:!this.singleLoop,autoplay:this.autoplay}};assert(this.worker_);if(!this.hasTransferredCanvas_){message.canvas=this.offscreenCanvas_;this.hasTransferredCanvas_=true;this.worker_.postMessage(message,[this.offscreenCanvas_])}else{this.worker_.postMessage(message)}}onMessage_(event){if(event.data.name==="initialized"&&event.data.success){this.isAnimationLoaded_=true;this.sendPendingInfo_();this.fire("cr-lottie-initialized")}else if(event.data.name==="playing"){this.fire("cr-lottie-playing")}else if(event.data.name==="paused"){this.fire("cr-lottie-paused")}else if(event.data.name==="stopped"){this.fire("cr-lottie-stopped")}else if(event.data.name==="complete"){this.fire("cr-lottie-complete")}else if(event.data.name==="resized"){this.fire("cr-lottie-resized",event.data.size)}}sendPendingInfo_(){if(this.workerNeedsSizeUpdate_){this.workerNeedsSizeUpdate_=false;this.sendCanvasSizeToWorker_()}if(this.workerNeedsPlayControlUpdate_){this.workerNeedsPlayControlUpdate_=false;this.sendPlayControlInformationToWorker_()}}}customElements.define(CrLottieElement.is,CrLottieElement);  ghes: '*'
  ghec: '*'
topics:
  - Accounts
  - CLI
  - Mobile
  - Desktop
  - Security
---

## About accounts

With {% data variables.product.github %}, you can store and collaborate on code. Accounts allow you to organize and control access to that code. There are three types of accounts on {% data variables.product.github %}.
* User accounts
* Organization accounts
* Enterprise accounts

Every person who uses {% data variables.product.github %} signs in to a user account. An organization account enhances collaboration between multiple users, and {% ifversion fpt or ghec %}an enterprise account{% else %}the enterprise account for {% data variables.location.product_location %}{% endif %} allows central management of multiple organizations.

## User accounts

{% data reusables.accounts.your-personal-account %}

Your user account can own resources such as repositories, packages, and projects. Any time you take any action on {% data variables.product.github %}, such as creating an issue or reviewing a pull request, the action is attributed to your user account.

User accounts are intended for humans, but you can create accounts to automate activity on {% data variables.product.github %}. This type of account is called a machine user. For example, you can create a machine user account to automate continuous integration (CI) workflows.

{% ifversion fpt or ghec %}

There are two types of user account:

* [Personal accounts](#personal-accounts)
* [{% data variables.enterprise.prodname_managed_users_caps %}](#managed-user-accounts)

### Personal accounts

If you signed up for your own account on {% data variables.product.prodname_dotcom_the_website %}, you are using a personal account.

Each personal account uses either {% data variables.product.prodname_free_user %} or {% data variables.product.prodname_pro %}. All personal accounts can own an unlimited number of public and private repositories, with an unlimited number of collaborators on those repositories. If you use {% data variables.product.prodname_free_user %}, private repositories owned by your personal account have a limited feature set. You can upgrade to {% data variables.product.prodname_pro %} to get a full feature set for private repositories. For more information, see [AUTOTITLE](/get-started/learning-about-github/githubs-plans).

Many people use one personal account for all their work on {% data variables.product.prodname_dotcom_the_website %}, including both open source projects and paid employment. If you're currently using more than one personal account that you created for yourself, we suggest combining the accounts. For more information, see [AUTOTITLE](/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-your-personal-account/merging-multiple-personal-accounts).

Even if you're a member of an organization that uses SAML single sign-on, you will still sign in to your own personal account on {% data variables.product.prodname_dotcom_the_website %}, and that personal account will be linked to your identity in your organization's identity provider (IdP). For more information, see [AUTOTITLE](/enterprise-cloud@latest/authentication/authenticating-with-saml-single-sign-on/about-authentication-with-saml-single-sign-on){% ifversion fpt %}" in the {% data variables.product.prodname_ghe_cloud %} documentation.{% else %}.{% endif %}

### {% data variables.enterprise.prodname_managed_users_caps %}

If your account was created for you by an enterprise on {% data variables.product.prodname_ghe_cloud %}, you are using a {% data variables.enterprise.prodname_managed_user %}.

As a {% data variables.enterprise.prodname_managed_user %}:

* Some of your account details and settings are managed by your enterprise.
* You must sign in to your {% data variables.enterprise.prodname_managed_user %} to access organizations and repositories owned by the enterprise.
* You can create your own private repositories, but you cannot create public content or contribute to repositories outside the enterprise.

{% endif %}

## Organization accounts

Organizations are shared accounts where a large number of people can collaborate across many projects at once.

Like user accounts, organizations can own resources such as repositories, packages, and projects. However, you cannot sign in to an organization. Instead, each person signs in to their user account, and any actions the person takes on organization resources are attributed to their user account. Each user can be a member of multiple organizations.

The users within an organization can be given different roles in the organization, which grant different levels of access to the organization and its data. All members can collaborate with each other in repositories and projects, but only organization owners and security managers can manage the settings for the organization and control access to the organization's data with sophisticated security and administrative features. For more information, see [AUTOTITLE](/organizations/managing-peoples-access-to-your-organization-with-roles/roles-in-an-organization) and [AUTOTITLE](/organizations/keeping-your-organization-secure).

You can also create nested sub-groups of organization members called teams, to reflect your group's structure and simplify access management. For more information, see [AUTOTITLE](/organizations/organizing-members-into-teams/about-teams).

{% data reusables.organizations.organization-plans %}

For more information about all the features of organizations, see [AUTOTITLE](/organizations/collaborating-with-groups-in-organizations/about-organizations).

## Enterprise accounts

{% ifversion fpt %}
{% data variables.product.prodname_ghe_cloud %} and {% data variables.product.prodname_ghe_server %} include enterprise accounts, which allow administrators to centrally manage policy and billing for multiple organizations and enable innersourcing between the organizations. For more information, see [AUTOTITLE](/enterprise-cloud@latest/admin/managing-your-enterprise-account/about-enterprise-accounts) in the {% data variables.product.prodname_ghe_cloud %} documentation.
{% elsif ghec %}
Enterprise accounts allow central policy management and billing for multiple organizations. You can use your enterprise account to centrally manage policy and billing. Unlike organizations, enterprise accounts cannot directly own resources like repositories, packages, or projects. These resources are owned by organizations within the enterprise account instead. For more information, see [AUTOTITLE](/admin/managing-your-enterprise-account/about-enterprise-accounts).
{% elsif ghes %}
Your enterprise account is a collection of all the organizations {% ifversion ghes %}on{% endif %} {% data variables.location.product_location %}. You can use your enterprise account to centrally manage policy and billing. Unlike organizations, enterprise accounts cannot directly own resources like repositories, packages, or projects. These resources are owned by organizations within the enterprise account instead. For more information, see [AUTOTITLE](/admin/managing-your-enterprise-account/about-enterprise-accounts).
{% endif %}

## Further reading

{% ifversion fpt or ghec %}
* [AUTOTITLE](/get-started/start-your-journey/creating-an-account-on-github){% endif %}
* [AUTOTITLE](/organizations/collaborating-with-groups-in-organizations/creating-a-new-organization-from-scratch)
* [Organizing people for successful collaboration](https://vimeo.com/333786093) video in {% data variables.product.company_short %} Resources
