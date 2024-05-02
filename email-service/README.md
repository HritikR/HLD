# Email Service System

## Overview

The Email Service System is designed to handle the reception, queuing, and sending of emails efficiently. This system aims to provide scalable and reliable email delivery for web applications by separating the email receiving and sending processes into two distinct services.

## Components

1. **Email API Worker**: A lightweight service that receives email requests from clients and enqueues them for processing.
2. **Email Sender Service**: A dedicated backend service responsible for dequeuing email requests and sending them out through SMTP or email APIs.

## Technologies Used

- **Node.js**: The runtime environment for both the Email API Worker and the Email Sender Service.
- **Express**: A web framework for Node.js used to handle incoming HTTP requests in the Email API Worker.
- **RabbitMQ**: Message broker for queuing email requests between the Email API Worker and the Email Sender Service.
- **Nodemailer**: A module for Node.js to send emails easily via SMTP or other transport mechanisms.
- **Docker**: Used to containerize the Email Sender Service for easier deployment and scalability.
- **Cloudflare Workers**: Used to deploy the Email API Worker for handling requests at the edge.

## Data Flow

1. **Reception of Email Requests**:
   - Clients send email requests to the Email API Worker deployed on Cloudflare Workers.
   - The worker validates the requests and enqueues them in RabbitMQ.

2. **Email Processing**:
   - The Email Sender Service continuously polls RabbitMQ for new email tasks.
   - Once a task is received, the service uses Nodemailer to send the email through the configured SMTP service.
   - The result of the sending process (success or failure) is logged for record-keeping and debugging.

## Repository Structure

```plaintext
/email-service
│
├── /worker-api            # Cloudflare Worker for handling incoming email requests
│   ├── index.js           # Main entry point for the Cloudflare Worker
│   ├── package.json       # Dependencies and scripts for the worker
│   └── wrangler.toml      # Configuration for deployment with Wrangler
│
├── /email-sender          # Backend service for sending emails
│   ├── src
│   │   ├── index.js       # Main application script
│   │   ├── emailConsumer.js # Script to consume and process email requests
│   │   └── emailHelper.js # Helper functions for email sending logic
│   ├── package.json       # Node.js package file for dependencies and scripts
│   ├── Dockerfile         # Docker configuration for building the image
│   └── .env.example       # Example environment variables
│
└── README.md              # Documentation for the entire project
```

## Diagram
```plaintext
   +------------------+     +------------------+     +------------------+     +------------------+
   |                  |     |                  |     |                  |     |                  |
   |  Client Request  +----->  Email API Worker+----->  RabbitMQ Queue  +----->  Email Sender    |
   |                  |     |                  |     |                  |     |      Service     |
   +------------------+     +------------------+     +------------------+     +------------------+
                                                                                    |
                                                                                    v
                                                                               +------------------+
                                                                               |                  |
                                                                               |  SMTP/Email API  |
                                                                               |    (Nodemailer)  |
                                                                               |                  |
                                                                               +------------------+
```

