## Int32、Int64

C#中可用Int32和Int64直接指定32位或64位整型，Int64相当于long

## Int数据位操作函数

```C#
public static Int32 SetBitValueInt32(Int32 value, ushort index, bool bitValue)
        {
            if (index > 31) throw new ArgumentOutOfRangeException("index"); //索引超出范围
            var val = 1 << index;
            return bitValue ? (value | val) : (value & ~val);   //置1：与1或，置0：与0与
        }

        public static Int64 SetBitValueInt64(Int64 value, ushort index, bool bitValue)
        {
            if (index > 60) throw new ArgumentOutOfRangeException("index"); //索引超出范围
            var val = 1 << index;
            return bitValue ? (value | val) : (value & ~val);   //1和0/1位或都是1,0和0/1位与都是0
        }

        //位为1返回true
        public static bool GetBitValueInt32(Int32 value, ushort index)
        {
            if (index > 31) throw new ArgumentOutOfRangeException("index"); //索引出错
            var val = 1 << index;
            return (value & val) == val;        //某bit和1与，若改变则为0，若不变则为1
        }

        public static bool GetBitValueInt64(Int64 value, ushort index)
        {
            if (index > 60) throw new ArgumentOutOfRangeException("index"); //索引出错
            var val = 1 << index;
            return (value & val) == val;
        }

        public static void SetInt32AllBit1(ref Int32 value)
        {
            //for(ushort i = 0; i < 32; i++)
            //{
            //    value = SetBitValueInt32(value, i, true);
            //}
            value = -1;
        }

        public static void SetInt32AllBit0(ref Int32 value)
        {
            //for (ushort i = 0; i < 32; i++)
            //{
            //    value = SetBitValueInt32(value, i, false);
            //}
            value = 0;
        }

        public static void SetInt64AllBit1(ref Int64 value)
        {
            //for (ushort i = 0; i < 64; i++)
            //{
            //    value = SetBitValueInt64(value, i, true);
            //}
            value = -1;
        }

        public static void SetInt64AllBit0(ref Int64 value)
        {
            //for (ushort i = 0; i < 64; i++)
            //{
            //    value = SetBitValueInt64(value, i, false);
            //}
            value = 0;
        }
```

