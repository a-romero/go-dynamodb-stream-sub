# go-dynamodb-stream-sub
Go channel for streaming Dynamodb Updates

```go
package main

import (
	"fmt"
	"github.com/aws/aws-sdk-go/aws"
	"github.com/aws/aws-sdk-go/aws/session"
	"github.com/aws/aws-sdk-go/service/dynamodb"
	"github.com/aws/aws-sdk-go/service/dynamodbstreams"
	"github.com/a-romero/go-dynamodb-stream-sub/stream"
)

func main() {
	cfg := aws.NewConfig().WithRegion("eu-west-1")
	sess := session.New()
	streamSvc := dynamodbstreams.New(sess, cfg)
	dynamoSvc := dynamodb.New(sess, cfg)
	table := "tableName"

	streamSubscriber := stream.NewStreamSubscriber(dynamoSvc, streamSvc, table)
	ch, errCh := streamSubscriber.GetStreamDataAsync()

	go func(errCh <-chan error) {
		for err := range errCh {
			fmt.Println("Stream Subscriber error: ", err)
		}
	}(errCh)

	for record := range ch {
		fmt.Println("from channel:", record)
	}
}
```
