# Creating your own credentials.env and RAILS_MASTER_KEY

## How the credentials file works
Rails applications all you to keep essential secret values (such as "secret_key_base", database passwords, or API keys) in an encrypted file called `credentials.enc`.  Much like a personal password manager, this file can hold many secrets, but is unlocked by just one master password.  This allows you to store secrets -- and even commit them to version control -- safely, as long as you never commit the RAILS_MASTER_KEY.  

Then, deploying your app doesn't need to involve setting a lot of environment variables (ie `MY_DATABASE_PASSWORD`); you can just set  `RAILS_MASTER_KEY` and let the credentials file provide all the values inside.

For this demo app, rather than share my master key, I'm going to walk you through recreating the credentials file locally.

## Step 1: Delete Existing Credentials

First, remove the existing credentials file:

```
rm config/credentials.yml.enc
```

## Step 2: Generate a new secret_key_base
For the Elastic Beanstalk demo, the only secret value you need to set inside the credentials file is called `secret_key_base`.  You'll need a value for that key, which can be generated by running this.  Note that you'll need to have Rails installed locally for the next few commands.  Run the following and **copy this value to your clipboard**.
```
rails secret
```

## Step 4: Generate New Credentials and Master Key

Run the following command to generate a new credentials.enc file and a new master key.  You may substitute `vi` with your favorite command-line editor.

```
EDITOR="vi --wait" bin/rails credentials:edit
```

This will open a new credentials file in your specified editor.

## Step 5: Add secret_key_base to Credentials

In the opened file, add this line to set `secret_key_base`.

```
secret_key_base: <your_newly_generated_secret_key_base>
```

Save and quit.  Commit credentials.enc to your git repo.  **DO NOT commit config/master.key**.

## Step 6: Obtain (and push) your new master key 

You can view your new master key in plaintext in `config/master.key`.  If you are doing the Elastic Beanstalk tutorial, you can push it to your environment with this command:

```
eb setenv RAILS_MASTER_KEY=$(cat config/master.key)
```

If you have multiple EB environments, remember to include the name of your environment with `-e <environment_name>`.


# Building and Running Locally with Docker

Make sure you are running docker on your machine, then..

```
docker build -t my-art-gallery .
```

When that is complete, run it with this:
```
docker run -p 3000:3000 -e RAILS_MASTER_KEY=$(cat config/master.key) my_art_gallery_copilot
```

and access it at http://localhost:3000.


