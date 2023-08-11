# Developers Blog

This is a study blog for LudaSoft developers to enhance their technical skills.

1.  Plan

            - Zeans
              - Fundamentals and Theory
              - Deep Learning Model Architectures
              - Data Preprocessing and Feature Extraction
              - Model Training and Evaluation
              - Applications of Deep Learning
            - Hung
            - Anh \* homePage: https://react-hook-form.com/
              Tuần 1:
              Đọc tài liệu về "react-hook-form" và tìm hiểu cách nó hoạt động .Thực hành với ví dụ cơ bản về "react-hook-form"

              Tuần 2:
              Bắt đầu thực hành và kiểm soát dữ liệu đầu vào của các phương thức :
              Tuần 3:
              Tùy chỉnh và tích hợp :

                  1.Học cách tùy chỉnh giao diện người dùng và tích hợp với các thư viện bên thứ ba để xử lý các trường biểu mẫu.

              Tuần 4:
              Áp dụng vào project thực tế :

                  1.Áp dụng react-hook-form vào project RAC.

    // Phần 2 : Hướng dẫn sử dụng AutoSelect và cách sử dụng

    => Lưu ý : - name (string, bắt buộc): Tên của trường dữ liệu trong react-hook-form.

         - options (Option[], bắt buộc): Mảng các tùy chọn cho dropdown.

         - control (any, bắt buộc): Đối tượng control từ react-hook-form.

         - customStyle (React.CSSProperties, tùy chọn): Định dạng CSS tùy chỉnh cho component.

         - placeholder (string, tùy chọn): Văn bản gợi ý cho dropdown.

    => Cách sử dụng autoSelect

            const options: Option[] = [
              { value: 1, label: "Option 1" },
              { value: 2, label: "Option 2" },
              { value: 3, label: "Option 3" },
              ];

              const App: React.FC = () => {
              const { control, handleSubmit } = useForm();

              const onSubmit = (data: any) => {
              console.log(data);
              };

              return (
              <form onSubmit={handleSubmit(onSubmit)}>
                    <SelectComponent
                    name="mySelect"
                    options={options}
                    control={control}
                    customStyle={{ width: "200px" }}
                    placeholder="Select an option"
                    />
                    <button type="submit">Submit</button>
              </form>
              );
              };

        Trong này có 2 cái quan trọng cần chú ý đó là Name và ID, nó tương đương với 2 giá trị bên trong options
          Name thì dùng để hiển thị lên view cho người dùng xem
          ID thì sẽ getVlaue khi chúng ta call API để submit ( name="mySelect" ). mySelect sẽ chứa giá trị để gửi lên API .

    // Phần 3 : Hướng dẫn sử dụng NumberCellRenderer

          NumberCellRenderer là một component React được sử dụng để hiển thị các giá trị số trong các ô của bảng dữ liệu.

        1.value (required): Giá trị số cần hiển thị.

        2.format (optional): Chuỗi định dạng để định dạng số theo yêu cầu. Nếu không được cung cấp, giá trị số sẽ được hiển thị dưới dạng văn bản thông thường.

        exemple:

          {
          field: "positionName",
          headerName: "직위",
          width: 120,
          // editable: true,
          cellRenderer: (params: any) =>
          NumberCellRenderer({ value: params.getValue(), format: "0,0" }),
          },

          {
          field: "teamName",
          headerName: "소속팀",
          width: 160,
          // editable: true,
          cellRenderer: (params: any) =>
          NumberCellRenderer({ value: params.getValue(), format: "0,0.00" }),

    },

2.  Performance
