---
layout: post
title: React Form Hook
excerpt: "Form React vs Formik vs React Form Hook"
tags: [react, code, form]
modified: 2020-06-19
comments: true
image:
  feature: react-hook-form.png
---

Làm việc với form và một trong những vấn để thử thách nhất khi phát triển ứng dụng bằng React. React là một thư viện tối giản chỉ tập trung vào việc kiểm soát sự thay đổi của giao diện, đảm bảo rằng giao diện người dùng sẽ thay đổi phù hợp với hoạt động họ. Nó không cung cấp một giải pháp hoàn chỉnh nào cho việc sử dụng input nhưng nó cung cho ta phương thức để lưu giá trị của input vào state.

Việc xử lý form với React yêu cầu bạn phải viết một số đoạn mã phục vụ việc:
1. Quản lý vào kiểm soát giá trị input mà bạn lưu vào state
2. Theo dõi error messages
3. xử lý submission

Để việc xử lý form được đơn giản hơn, nhiều developer đã tạo ra các thư viện giúp build form với React. Một trong những thư viện phổ biến nhất là [Formik](https://jaredpalmer.com/formik/) 

Bản thân tôi đã từng sử dụng thư viện này như một giải pháp khi làm việc với Form của React và nó hoạt động thực sự tuyệt vời, Nhưng gần đây tôi đang làm việc với một thư viện mới có tên là [React Hook Form](https://react-hook-form.com/)

Trong bài viết này tôi sẽ cho bạn thấy được lợi ích của việc sử dụng `React Hook Form` hơn là sử dụng `Formik`. Nhưng đầu tiên hãy tìm hiểu làm thế nào để tạo ra Form bằng React Form.

### How to handle forms with React

{% highlight js %}
  import React, { useState } from "react";
  import "bootstrap/dist/css/bootstrap.css";

  export default () => {
    const [formState, setFormState] = useState({
      formValues: {
        email: "",
        password: ""
      },
      formErrors: {
        email: "",
        password: ""
      },
      formValidity: {
        email: false,
        password: false
      }
    });

    const handleChange = ({ target }) => {
      const { formValues } = formState;
      formValues[target.name] = target.value;
      setFormState({ formValues });
      handleValidation(target);
    };

    const handleValidation = target => {
      const { name, value } = target;
      const fieldValidationErrors = formState.formErrors;
      const validity = formState.formValidity;
      const isEmail = name === "email";
      const isPassword = name === "password";
      const emailTest = /^[^\s@]+@[^\s@]+\.[^\s@]{2,}$/i;

      validity[name] = value.length > 0;
      fieldValidationErrors[name] = validity[name]
        ? ""
        : `${name} is required and cannot be empty`;

      if (validity[name]) {
        if (isEmail) {
          validity[name] = emailTest.test(value);
          fieldValidationErrors[name] = validity[name]
            ? ""
            : `${name} should be a valid email address`;
        }
        if (isPassword) {
          validity[name] = value.length >= 3;
          fieldValidationErrors[name] = validity[name]
            ? ""
            : `${name} should be 3 characters minimum`;
        }
      }

      setFormState({
        ...formState,
        formErrors: fieldValidationErrors,
        formValidity: validity
      });
    };

    const handleSubmit = event => {
      event.preventDefault();
      const { formValues, formValidity } = formState;
      if (Object.values(formValidity).every(Boolean)) {
        // Form is valid
        console.log(formValues);
      } else {
        for (let key in formValues) {
          let target = {
            name: key,
            value: formValues[key]
          };
          handleValidation(target);
        }
      }
    };

    return (
      <div className="container">
        <div className="row mb-5">
          <div className="col-lg-12 text-center">
            <h1 className="mt-5">React regular form</h1>
          </div>
        </div>
        <div className="row">
          <div className="col-lg-12">
            <form onSubmit={handleSubmit}>
              <div className="form-group">
                <label>Email address</label>
                <input
                  type="email"
                  name="email"
                  className={`form-control ${
                    formState.formErrors.email ? "is-invalid" : ""
                  }`}
                  placeholder="Enter email"
                  onChange={handleChange}
                  value={formState.formValues.email}
                />
                <div className="invalid-feedback">
                  {formState.formErrors.email}
                </div>
              </div>
              <div className="form-group">
                <label>Password</label>
                <input
                  type="password"
                  name="password"
                  className={`form-control ${
                    formState.formErrors.password ? "is-invalid" : ""
                  }`}
                  placeholder="Password"
                  onChange={handleChange}
                  value={formState.formValues.password}
                />
                <div className="invalid-feedback">
                  {formState.formErrors.password}
                </div>
              </div>
              <button type="submit" className="btn btn-primary btn-block">
                Submit
              </button>
            </form>
          </div>
        </div>
      </div>
    );
  };

{% endhighlight %}

trong ví dụ trên đay, chúng ta đã tạo 1 function component với 1 state có tên là formState nơi mà chúng ta lưu trữ state của input value, input validity và input errors. Form có 2 input là `email` và `password` và chúng ta cần khởi tạo giá trị ban đầu cho chúng.

Tiếp theo chúng ta viết phần render cho Form, ta sửa dụng formValues để hiển thị giá trị của input trong khi formErrors dùng để chỉ ra các gía trị của form invalid.

Tiếp theo chúng ta tạo ra function handleChange, function này sẽ update state value và chạy function handleValidation để kiểm tra giá trị mới. Function handleValidation là nơi mọt thứ trở nên phức tạp, đầu tiên nó cần kiểm tra input có hợp lệ và sau đó tiến hành xác nhận hợp lệ. Nếu là email field, nó phải kiểm tra định dạng email, nếu là password field nó phải kiểm trả tối thiểu 3 ký tự. Sau nó lưu message error vào formErrors và đánh dấu hợp lệ bằng formValidity.

Cuối cùng ta cần viết function `handleSubmit`.

Khi form valid, ta log giá trị. Nếu không thì chạy function handleValidation cho mỗi giá bên trong formValues object. React sẽ cho người dùng biết rằng họ đang sai ở giá trị nào.

Và đó là cách để xây dựng form với React. Như bạn vừa thấy chỉ với 2 field cho một form thì đoạn code trên đã thực sự rất dài dòng. Thử tưởng tượng xem nó sẽ thế nào nếu bạn phải xử lý với form có hàng tá field.

Giờ thì bạn đã biết được cách xây dựng form của React và kết quả của nó là code rất dài dòng, lặp đi lặp lại tốn thời gian. Giờ thì bạn đã sẵn sàng để tìm hiểu cách mà Formik giảm thiểu những phiền phức mà bạn gặp phải.

### This is how Formik can help you

Formik là một thư viện xử lý form phổ biến bởi vì nó cung cấp cho bạn form component có thể tái sử dụng, nơi mà bạn có thể đơn giản sử dụng API của nó để xử lý 3 vấn đề phiền phức nhấy khi xây dựng form.

1 Lưu giá trị vào form
2 Validation và error messages
3 Xử lý submission

Để bắt đầu với formik thì trước tiên ta cần phải cài đặt nó, bạn có thể cài đặt thông qua npm hoặc yarn command `yarn add formik`, Dưới đây là form tương tự với 2 field, được xây dựng bởi formik.

{% highlight js %}
  import React from "react";
  import { Formik, Form, Field, ErrorMessage } from "formik";

  function validateEmail(value) {
    let error;
    if (!value) {
      error = "Email is required";
    } else if (!/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)) {
      error = "Invalid email address format";
    }
    
    return error;
  }

  function validatePassword(value) {
    let error;
    if (!value) {
      error = "Password is required";
    } else if (value.length < 3) {
      error = "Password must be 3 characters at minimum";
    }
    
    return error;
  }

  export default () => {
    const onSubmit = values => {
      // form is valid
      console.log(values);
    }

    return (
      <div className="container">
        <div className="row mb-5">
          <div className="col-lg-12 text-center">
            <h1 className="mt-5">Login form with Formik</h1>
          </div>
        </div>
        <Formik
          initialValues={{
            email: "",
            password: ""
          }}
          onSubmit={onSubmit}
        >
          {({ errors, touched }) => (
              <Form>
                <div className="form-group">
                  <label htmlFor="email">Email</label>
                <Field 
                  name="email"
                  placeholder="Enter email"
                  className={`form-control ${
                    touched.email && errors.email ? "is-invalid" : ""
                  }`}
                  validate={validateEmail} />
                <ErrorMessage
                  component="div"
                  name="email"
                  className="invalid-feedback"
                />
                </div>

                <div className="form-group">
                  <label htmlFor="password">Password</label>
                <Field 
                  name="password"
                  type="password"
                  placeholder="Enter password"
                  className={`form-control ${
                    touched.password && errors.password ? "is-invalid" : ""
                  }`}
                  validate={validatePassword} />
                <ErrorMessage
                  component="div"
                  name="password"
                  className="invalid-feedback"
                />
                </div>

                <button className="btn btn-primary btn-block" type="submit">Submit</button>
              </Form>
            )}
        </Formik>
      </div>
    );
  };
{% endhighlight %}

Form mới của chúng ta sử dụng 4 component được cung cấp bởi Formik: Formik, Form, Field, và ErrorMessage. Để sử dụng Formik, ta cần đặt component Form bên trong Formik component.

{% highlight js %}

<Formik>
  <Form>
    {/* the rest of the code here */}
  </Form>
</Formik>

{% endhighlight %}

Tương tự như Form bình thường, ta phải khởi tạo giá trị ban đầu cho form với state, nhưng thay vì viết toàn bộ value, validation, validity ta chỉ đơn giản sử dụng thuộc tính initialValues của Formik. Formik component củng đồng thời nhận vào 1 function prop để xử lý sự kiện submit. Vì vậy hãy viết function onSubmit, function này chỉ chạy khi các giá trị của form valid.

Tiếp theo ta viết form sử dụng Form component, đầu tiên ta truyền props từ formik vào Form, chúng ta nhận errors và touched props và có thể biết được khi nào field được touch hoặc chứa lỗi.

ErrorMessage component từ Formik sẽ tự động hiển thị bất kì message lỗi nào từ props erros bằng việc matching name của Field componet.

Hàm validation của Formik đơn giản truyền vào giá trị của field , nếu giá trị không hợp lệ, formik return message lỗi và trả về undefined nếu ngược lại.

Và như vậy là việc xây dựng form bằng `Formik` đã hoàn thành, bạn vừa học được cách mà formik đã tối giản số lượng code mà bạn phải viết để xây dựng form. Thật tuyệt vời! Formik thực sự dễ dàng hơn nhiều so với regular form của React. Vậy `React Hook Form` có thực sự mang lại nhiều lợi ích hơn so với Formik. Thực ra React Hook Form còn tốt hơn nhiều so với Formik.

### Enter React Hook Form

Giống như formik, React Hook Form là một thư viện xây dựng form mục đích giảm thiểu sự phiền phức khi làm việc với form của React. Điểm khác biệt lớn giữa hai loại này là React Hook Form được thiết kế để sử dụng các component không được kiểm soát để trách việc re-render không cần thiết do người dùng nhập input.

{% highlight js %}

  import React from "react";
  import { useForm, ErrorMessage } from "react-hook-form";
  import "bootstrap/dist/css/bootstrap.css";

  export default () => {
    const { register, errors, handleSubmit } = useForm();
    const onSubmit = values => {
      // form is valid
      console.log(values);
    }

    return (
      <div className="container">
        <div className="row mb-5">
          <div className="col-lg-12 text-center">
            <h1 className="mt-5">Login form with React Hook Form</h1>
          </div>
        </div>
      <form onSubmit={handleSubmit(onSubmit)}>
        <div className="form-group">
          <label htmlFor="email">Email</label>
          <input
            name="email"
            placeholder="Enter email"
            className={`form-control ${
              errors.email ? "is-invalid" : ""
            }`}
            ref={register({
              required: "Email is required",
              pattern: {
                value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i,
                message: "Invalid email address format"
              }
            })}
          />
          <ErrorMessage className="invalid-feedback" name="email" as="div" errors={errors} />
        </div>

        <div className="form-group">
          <label htmlFor="password">Password</label>
        <input
          name="password"
          placeholder="Enter password"
            className={`form-control ${
              errors.password ? "is-invalid" : ""
            }`}
          ref={register({
            required: "Password is required",
            validate: value => value.length < 3 || "Password must be 3 characters at minimum"
          })}
        />
        <ErrorMessage className="invalid-feedback" name="password" as="div" errors={errors} />
        </div>

        <button className="btn btn-primary btn-block" type="submit">Submit</button>
      </form>
      </div>
    );
  };

{% endhighlight %}
Để bắt đầu sử dụng `React Hook Form`, ta cần cài nó thông qua npm hoặc yarn command `yarn add react-hook-form`.  Tiếp theo ta cần import useForm hook, cái sẽ xử lý việc nhận input, validation và submit form. và ErrorMessage component tương tự Formik.

Đầu tiên, ta gọi `useForm` hook, thứ sẽ trả về một object với những function cho việc build form.

Ý tường cốt lỗi của React Form Hook là `register` component không được kiểm soát vào hook bằng việc sử dụng `ref` prop. Nó làm cho values có thể sử dụng ở cả form validation và submission.

Hàm `register` củng cung cấp việc tạo các validation rule cho input. React Hook Form validation rule được thiết kế đơn giản tương tự HTML validation. Bất kì message nhận được sẽ truyền vào errors object. Chúng ta sẽ truyền errros object vào `ErrorMessage` Component, nó sẽ matching errors object với name prop của nó.

Cuối cùng ta cần truyền `handleSubmit` vào onSubmit prop. Và vậy là hoàn thành. Ngoài việc giảm thiểu các dòng code, `React Hook Form` còn có hiệu năng tốt hơn thông qua việc giảm thiểu việc re-render.

### Conclusion

Thông qua bài viết, bạn đã nắm được những rắc rối khi xây dựng form với React và cách mà những thư viện như `Formik` và `React Hook Form` giải quyết bài toán này. Cả hai đều cùng giải một bài toán nhưng cách của React Hook Form cho phép nó đạt kết quả tốt hơn so với Formik. Ngoài ra bạn không cần import bất kì component nào ngoài việc viết cả input với prop ref. Nếu bạn đang tìm kiếm một thư viện để xây dựng form, tôi đề xuất bạn sử dụng `React Form Hook`.
