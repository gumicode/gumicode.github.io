---
layout: default
title: Spring Context
has_children: true
nav_order: 100000
last_modified_date: 2022-07-10 00:00:00
last_modified_at: 2022-07-10 00:00:00
---

# Spring Context
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

## 대표적인 기능

### Converter

### Fomatter

### ReloadableResourceBundleMessageSource

어플리케이션 실행중 메세지 리소스를 Reload 할수 있다.

```java
    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
        messageSource.setBasenames(names);
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }
```