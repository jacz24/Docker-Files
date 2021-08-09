# Docker File to containerize the processor for quick deployment

#FROM python:3.8-slim-buster

FROM ubuntu:20.04 as base

FROM base as builder

ENV LANG=C.UTF-8


ADD . /home/ogoun


ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
  build-essential autoconf automake libtool \
  libleptonica-dev \
  zlib1g-dev \
  python3-dev \
  python3-distutils \
  libffi-dev \
  libqpdf-dev \
  ca-certificates \
  curl \
  git

RUN apt-get update && apt-get install -y --no-install-recommends apt-utils
RUN apt-get install -y 'ffmpeg'\
    'jbig2dec'\
    'ocrmypdf'\
    'imagemagick'\
    'libmagickwand-dev'\
    'libsm6'\
    'libxext6'\
    'gcc'\
	'poppler-utils'\
    'ghostscript'\
    'tesseract-ocr'\
    'libtesseract-dev'  -y

# Get the latest pip (Ubuntu version doesn't support manylinux2010)
RUN \
	curl https://bootstrap.pypa.io/get-pip.py | python3

RUN \
  mkdir jbig2 \
  && curl -L https://github.com/agl/jbig2enc/archive/ea6a40a.tar.gz | \
  tar xz -C jbig2 --strip-components=1 \
  && cd jbig2 \
  && ./autogen.sh && ./configure && make && make install \
  && cd .. \
  && rm -rf jbig2

COPY . /DijiProcessorCode

WORKDIR /DijiProcessorCode

#RUN pip3 install --no-cache-dir \
 # -r requirements/main.txt \
 # -r requirements/webservice.txt \
 # -r requirements/test.txt \
 # -r requirements/watcher.txt \
  #.

RUN apt-get update && apt-get install -y --no-install-recommends \
  ghostscript \
  img2pdf \
  liblept5 \
  libsm6 libxext6 libxrender-dev \
  zlib1g \
  pngquant \
  python3 \
  qpdf \
  tesseract-ocr \
  tesseract-ocr-chi-sim \
  tesseract-ocr-deu \
  tesseract-ocr-eng \
  tesseract-ocr-fra \
  tesseract-ocr-por \
  tesseract-ocr-spa \
  unpaper \
  && rm -rf /var/lib/apt/lists/*

COPY requirements.txt /tmp/
RUN pip install --requirement /tmp/requirements.txt
COPY . /tmp/

ENV NAME Processor

RUN mv /etc/ImageMagick-6/policy.xml /etc/ImageMagick-6/policy-disable.xml

ARG imagemagic_config=/etc/ImageMagick-6/policy.xml

RUN if [ -f $imagemagic_config ] ; then sed -i 's/<policy domain="coder" rights="none" pattern="PDF" \/>/<policy domain="coder" rights="read|write" pattern="PDF" \/>/g' $imagemagic_config ; else echo did not see file $imagemagic_config ; fi

#RUN echo "Listing project directory for debugging purposes"
#CMD ["pwd"]
#CMD ["ls"]


#RUN echo "Running ServerListener!"
CMD ["/bin/bash"]
# TODO EVERYTHING BELOW IS NOT WOrking
COPY pythonbootstrap.sh /DijiProcessorCode
RUN chmod +x pythonbootstrap.sh
CMD ["./pythonbootstrap.sh"]