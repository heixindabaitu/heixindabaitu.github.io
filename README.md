## Welcome to My Pages

GUI 文件管理 

### 具体功能如下：
1.窗口建立
2.实现文件查找、遍历
3.实现重命名
4.实现预览和统计
5.双击获得路径

### 主要代码：
import java.awt.*;

import java.awt.event.ActionEvent;

import java.io.File;

import javax.swing.*;

public class winsEvent {

	JFrame frame;//窗体
	JPanel jPanel;//面板
	Container container;//容器
	JTextField field1;//文本框提供输入路径
	JButton bsearch,breturn,bcopy,bstick,bname,bpreview,brecord;//查找，返回，复制，粘贴，重命名，预览，统计
	MyListener listener; 
	JList list;
	JTextArea area;//文本区显示预览和统计
	public winsEvent() {
		frame = new JFrame("文件管理器");
		frame.setBounds(200, 100, 800, 600); // 设置窗口大小和位置
		frame.setLayout(new BorderLayout());
		container = frame.getContentPane();//设置容器
		jPanel = new JPanel(); // 创建面板
		jPanel.setLayout(new FlowLayout(FlowLayout.LEADING));	
		first();//窗体显示和关闭	
		frame.setVisible(true);
		frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
	void first() {
		//定义
		field1 = new JTextField(10);
		field1.setText("");
		bsearch = new JButton("查找");
		bsearch.setActionCommand("bsearch");
		breturn = new JButton("返回");
		breturn.setActionCommand("breturn");
		list = new JList();
		bcopy = new JButton("复制");
		bcopy.setActionCommand("bcopy");
		bstick = new JButton("粘贴");
		bstick.setActionCommand("bstick");
		bname = new JButton("重命名");
		bname.setActionCommand("bname");
		area = new JTextArea(9,6);
		bpreview = new JButton("预览");
		bpreview.setActionCommand("bpreview");
		brecord = new JButton("统计");
		brecord.setActionCommand("brecord");
		//增加
		jPanel.add(field1);
		jPanel.add(bsearch);
		jPanel.add(breturn);
		jPanel.add(list);
		jPanel.add(bcopy);
		jPanel.add(bstick);
		jPanel.add(bname);
		jPanel.add(bpreview);
		jPanel.add(brecord);	
		container.add(jPanel, BorderLayout.NORTH);
		//滚动框
		JScrollPane jsp=new JScrollPane(list);
		container.add(jsp, BorderLayout.CENTER);
		container.add(new JScrollPane(area),BorderLayout.SOUTH);
	}
        void setMyListener(MyListener listener) {
        	this.listener = listener;
        	listener.setJTextField(field1);
        	listener.setJList(list);
        	listener.setJTextArea(area);
        	field1.addActionListener(listener);
        	bsearch.addActionListener(listener);
        	breturn.addActionListener(listener);
        	bcopy.addActionListener(listener);
        	bstick.addActionListener(listener);
        	bname.addActionListener(listener);
        	bpreview.addActionListener(listener);
        	brecord.addActionListener(listener);
        	list.addMouseListener(listener);
        	list.addListSelectionListener(listener);
        }
}
![FRAME](https://github.com/heixindabaitu/heixindabaitu.github.io/blob/main/%E7%AA%97%E5%8F%A3.png)

### 具体功能实现代码：

	public class Watch1 implements MyListener{

	JTextField put1;
	JTextArea out1;
	JList jlist;
	String str2;
	String copyPath = null; // 拷贝路径;
	Vector<String> vector = new Vector();
	@Override
	public void setJTextField(JTextField text) {
		put1 = text;
	}
	public void setJList(JList list) {
		jlist = list;
	}
	public void setJTextArea(JTextArea area) {
		out1 = area;
	}
	//返回路径
	public String returnText() {
		String str = put1.getText();
		return str;
	}
	
	@Override
	public void actionPerformed(ActionEvent e) {
		// TODO Auto-generated method stub
		//查找
		if (e.getActionCommand().equals("bsearch")) {
			System.out.println("查找");
			reFresh();
		}
		//返回
		if(e.getActionCommand().equals("breturn")) {
			String temp = str2.substring(0,
					str2.substring(0, str2.length() - 1).lastIndexOf("\\") + 1);
			System.out.println(temp);
			File f = new File(temp);
			if (f.isDirectory()) {
				str2 = temp;
				vector = fileList(str2);		//要遍历的路径  
				jlist.setListData(vector);
			}
		}
		
		//重命名
		if(e.getActionCommand().equals("bname")) {
			System.out.println("重命名");

			if (jlist.getSelectedValue() != null) {
				String newName = JOptionPane.showInputDialog("请输入修改的文件名");
				if (newName != null) {
					String selectFile = jlist.getSelectedValue().toString();
					if (new File(str2 + selectFile).exists()) {
						renameFile(str2, selectFile, newName);
						vector = fileList(str2);		//要遍历的路径  
						jlist.setListData(vector);
					}
				}
			}
		}
		//预览
		if(e.getActionCommand().equals("bpreview")) {
			System.out.println("预览");
			String selectFile = jlist.getSelectedValue().toString();
			File file = new File(str2 + selectFile);
	        if (file.isFile() && file.exists()) {
	            try {
	                InputStream stream = new FileInputStream(file);
	                InputStreamReader read = new InputStreamReader(stream);
	                BufferedReader bfReader = new BufferedReader(read);
	                String a = null;
	                while ((a = bfReader.readLine()) != null) {
	                    out1.append("预览："+a+"\n");
	                }
	                bfReader.close();
	            } catch (FileNotFoundException e1) {
	                e1.printStackTrace();
	            } catch (IOException e1) {
	                e1.printStackTrace();
	            }
	        }
	        else {
	        	out1.append("文件不存在");
	        }
		}
		//统计
		if(e.getActionCommand().equals("brecord")) {
			String selectFile = jlist.getSelectedValue().toString();
			File folder = new File(str2 + selectFile);
	        File []list = folder.listFiles();
	        int fileCount = 0, folderCount = 0;
	        long length = 0;
	        for (File file : list){
	            if (file.isFile()){
	                fileCount++;
	                length += file.length();
	            }else {
	                folderCount++;
	            }
	        }
	        out1.append("文件夹的数目: " + folderCount + " 文件的数目: " + fileCount+"\n");
		}
		
		
		
		
	}
	//鼠标双击进入文件夹
	@Override
	public void mouseClicked(MouseEvent e) {
		// TODO Auto-generated method stub
		if (e.getClickCount() == 2) {
			String temp = str2 + jlist.getSelectedValue();//文本框路径+列表区文件路径
			File f = new File(temp);//打开文件
			if (f.isDirectory()) {
				str2 = str2 + jlist.getSelectedValue();
				vector = fileList(str2);		  
				jlist.setListData(vector);
			}
		}
	}
	//返回路径
	public void reFresh() {
		str2=returnText();//传参返回文本框路径
		vector = fileList(str2);	
		jlist.setListData(vector);
	}
	
	

	//获取文件目录
	public static Vector<String> fileList(String path){//传入路径
		Vector<String> vector = new Vector();//定义数组
		
		File[] fl = new File(path).listFiles();//打开文件
		for (int i = 0; i < fl.length; i++) {
			if (fl[i].isFile()) {
				vector.add(fl[i].getName());
			}
			else if (fl[i].isDirectory()) {
				vector.add(fl[i].getName() + "\\");
			}
		}
		return vector;
	}
			// 文件重命名
			public static void renameFile(String path, String oldname, String newname) {
				if (!oldname.equals(newname)) {
					File oldfile = new File(path + "/" + oldname);
					File newfile = new File(path + "/" + newname);
					if (!oldfile.exists()) {
						return;
					}
					if (newfile.exists())
						System.out.println(newname + "文件名已经存在！");
					else {
						oldfile.renameTo(newfile);
					}
				} else {
					System.out.println("文件名未发生改变");
				}
			}	
	@Override
	public void mousePressed(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void mouseReleased(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void mouseEntered(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void mouseExited(MouseEvent e) {
		// TODO Auto-generated method stub
		
	}
	//显示当前鼠标点击路径
	@Override
	public void valueChanged(ListSelectionEvent e) {
		// TODO Auto-generated method stub
		if (e.getValueIsAdjusting()) {
			System.out.println(jlist.getSelectedValue());
		}
	}
	
	
}

