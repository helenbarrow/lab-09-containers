# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

The first reason why this is a bad idea is that anyone who can pull the publicly available image from ghcr.io could access the AWS credentials. Secondly, the AWS Learner Lab credentials are temporary and when the session ends, those credentials expire. If you include them in the image, it doesn't make them permanent, so the image would contain credentials that stop working.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The Docker command put the local ~/.aws into the container at /home/app/.aws which gave the container access to the AWS credentials and configuration files. get_atlas_settings() checks whether the Atlas settings are already provided via environment variables and since I didn't provide them, the function uses AWS instead. Then using the AWS credentials, the container retrieved the mongodb_credentials parameter to connect to Atlas and connect to MongoDB to save the analysis results.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

before: d4201ebb1340; after: c375888b8a9b
This is a problem because the tag 0.1.0 is supposed to identify a particular version, however when you rebuild, it gets replaced. This creates a reproducibility issue because others who download book-analysis:0.1.0 might receive a different image than someone who downloaded it earlier. Instead, you should assign the rebuilt image a new version tag (0.1.1).
