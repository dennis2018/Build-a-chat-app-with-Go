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

Let’s write the terminal commands to create a new folder in the src directory that is located in the $GOPATH, this folder will be the root directory for this project:

```
$ cd $GOPATH/src
    $ mkdir go-pusher-chat-app
    $ cd go-pusher-chat-app
```

In this folder, we will create the main Go file which will be the entry point for the application and call it chat.go. We also need to install the Go Pusher library that we will reference in the chat.go file.

Run the following code in the terminal to pull in the Go Pusher package:

```
  $ go get github.com/pusher/pusher-http-go
  
  ```
  
  Open the chat.go file in your IDE and paste the following code:
  
   ```
   // File: ./chat.go
    package main

    import (
        "encoding/json"
        "fmt"
        "io/ioutil"
        "log"
        "net/http"

        pusher "github.com/pusher/pusher-http-go"
    )

    var client = pusher.Client{
        AppId:   "PUSHER_APP_ID",
        Key:     "PUSHER_APP_KEY",
        Secret:  "PUSHER_APP_SECRET",
        Cluster: "PUSHER_APP_CLUSTER",
        Secure:  true,
    }

    type user struct {
        Name  string `json:"name" xml:"name" form:"name" query:"name"`
        Email string `json:"email" xml:"email" form:"email" query:"email"`
    }

    func main() {
        http.Handle("/", http.FileServer(http.Dir("./public")))

        http.HandleFunc("/new/user", registerNewUser)
        http.HandleFunc("/pusher/auth", pusherAuth)

        log.Fatal(http.ListenAndServe(":8090", nil))
    }
    
``` 

⚠️ Replace PUSHER_APP_* keys with the app credentials found on your Pusher dashboard.

In the code above, we first imported a list of packages then registered a new Pusher client with the credentials from the app we created earlier on the dashboard.

Next, we defined a user struct and included extra definitions to its properties so that Go knows how to handle incoming payloads and bind their various structures with a new instance of the user struct.

Lastly, in the main function, we registered three endpoints:

- / — returns the static files that define the view of the chat app. The static files will be served from a public directory.
- /new/user — creates a new user.
- /pusher/auth — authorizes users from the client-side so they can subscribe to private channels and trigger client events.

Each of the last two endpoints has an associated handler function that we will define below. Add the following code to the chat.go file before the main function:

``` 
    // File: ./chat.go

    // [...]

    func registerNewUser(rw http.ResponseWriter, req *http.Request) {
        body, err := ioutil.ReadAll(req.Body)
        if err != nil {
            panic(err)
        }

        var newUser user

        err = json.Unmarshal(body, &newUser)
        if err != nil {
            panic(err)
        }

        client.Trigger("update", "new-user", newUser)

        json.NewEncoder(rw).Encode(newUser)
    }

    func pusherAuth(res http.ResponseWriter, req *http.Request) {
        params, _ := ioutil.ReadAll(req.Body)
        response, err := client.AuthenticatePrivateChannel(params)
        if err != nil {
            panic(err)
        }

        fmt.Fprintf(res, string(response))
    }

    // [...]
``` 

In the registerNewUser function, we trigger a Pusher event, new-user, on the public channel update, so that the new user’s details are sent to the subscribed clients.

The syntax for triggering a Pusher event over a public channel in Go is:

``` 
 client.Trigger(channel, event, data)
 ``` 
