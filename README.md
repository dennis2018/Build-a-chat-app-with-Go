# Build-a-chat-app-with-Go

[Go](https://golang.org/) is an open source programming language that makes it easy to build simple, reliable, and efficient software. You will need Go 0.10.0+ installed on your machine. A basic knowledge of Go and JavaScript will be helpful.

Communication is an important part of the society we live in. Over the years, the forms of communication available have changed and have been refined to be both far-reaching and fast. With communication today, we can talk to people who are on the other side of the globe in an instant.

To power this sort of communication, there are some platforms that allow instant messaging such as Facebook, Twitter, Slack.

In this application, we will consider how to build a realtime chat application using Go, JavaScript and Pusher. 

## Prerequisites

You will need the following:
- An IDE of your choice like Visual Studio Code/Atom
- [Go](https://golang.org/) (version >= 0.10.x) installed on your computer. Here’s how you can install Go.
- Basic knowledge of the Go programming language
- Basic knowledge of JavaScript.

## Setting up Pusher

The realtime feature of this chat app will depend on Pusher Channels so you need to create an account [here](https://dashboard.pusher.com/accounts/sign_up) if you don’t already have one. After signing up, you will be asked to create a new application. Do so

## Enabling client events

We need to enable the Pusher application to trigger events from the client-side (browser) of the chat app. This is important because it is with this feature that users will be able to send private messages without hitting the backend server. Follow the steps below to activate client events from the [dashboard](https://dashboard.pusher.com/):

- Select the Channel application.
- Click on App Setting from the horizontal options list.
- Check the Enable client events option. 

![Image description](https://images.ctfassets.net/1es3ne0caaid/r2p5Sr6hLq4eeWMoII8YE/78f08355870ee274ed621530d5fd9f16/chat-app-go-enable-client-events.png)

- Click on the Update button.

## Setting up the codebase

et’s write the terminal commands to create a new folder in the src directory that is located in the $GOPATH, this folder will be the root directory for this project:

```
$ cd $GOPATH/src
    $ mkdir go-pusher-chat-app
    $ cd go-pusher-chat-app
```

